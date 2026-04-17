# webpack Dev Server — Custom Proxy with http-proxy-middleware

> Reference: `setupMiddlewares` · Mocks · Cookie Bootstrap · Auth

-----

## 1. Overview

webpack dev server uses `http-proxy-middleware` (HPM) internally. The built-in `proxy:` config exposes a simplified subset of HPM. By replacing it with a custom HPM instance via `setupMiddlewares` you get the full HPM v3 API while keeping HMR intact.

**Key point:** webpack dev server is NOT replaced — it continues to serve assets and HMR. Only the proxy layer changes.

-----

## 2. Built-in proxy vs HPM standalone

|Feature         |webpackDevServer                             |http-proxy-middleware                       |
|----------------|---------------------------------------------|--------------------------------------------|
|Hook naming     |`onProxyReq`, `onProxyRes`, `onError` (flat) |`on: { proxyReq, proxyRes, error }` (nested)|
|Multiple targets|Multiple keys in `proxy` object              |Multiple `app.use()` calls                  |
|Path matching   |String keys on `proxy` object                |First `app.use()` argument                  |
|Filter function |`context` key can be a function              |`filter` option or middleware composition   |
|Dynamic router  |Not supported                                |`router` option — target per request        |
|WebSocket proxy |`ws: true` on the rule                       |`ws: true` or `upgrade` handler separately  |
|Async `proxyReq`|Technically works but not awaited — race risk|Fully awaited — safe for async auth         |
|Error display   |Webpack overlay                              |Custom error handler required               |

### Hook naming (most common gotcha)

webpackDevServer still exposes the HPM v2 flat API. Standalone HPM v3 uses a nested `on:` object:

```js
// webpackDevServer (v2 style — still works here)
proxy: {
  '/api': {
    onProxyReq: (proxyReq, req, res) => { ... },
  }
}

// HPM v3 standalone (nested on:)
createProxyMiddleware({
  on: {
    proxyReq: (proxyReq, req, res) => { ... },
  },
})
```

-----

## 3. Replacing the proxy via `setupMiddlewares`

Remove the `proxy:` key from `webpackDevServer.config.js` entirely and use `setupMiddlewares` instead:

```js
// webpackDevServer.config.js
const { createProxyMiddleware } = require('http-proxy-middleware');
const { bootstrapProxy, apiProxy } = require('./config/devProxy');

module.exports = {
  // No proxy: key

  setupMiddlewares: (middlewares, devServer) => {

    // More-specific paths first
    devServer.app.use('/api/services/bootstrap', bootstrapProxy);
    devServer.app.use('/api', apiProxy);

    return middlewares; // webpack middleware runs after
  },
};
```

> **Always return `middlewares`.** Omitting it removes webpack’s own static-serving and HMR middleware from the stack.

### WebSocket proxying

HMR uses its own upgrade event. Guard your WS proxy with a path check so HMR’s upgrade is left alone:

```js
devServer.server.on('upgrade', (req, socket, head) => {
  if (req.url.startsWith('/ws')) {
    wsProxy.upgrade(req, socket, head);
  }
  // all other upgrades (HMR) pass through untouched
});
```

-----

## 4. Config file layout

Keep `webpackDevServer.config.js` thin. Proxy logic lives in `config/devProxy.js`:

```js
// config/devProxy.js
const { createProxyMiddleware } = require('http-proxy-middleware');

const TARGET = 'https://api.mybackend.com';

// ── Session store (process lifetime) ──────────────────────────────
let sessionCookies = [];

// ── Mock definitions ───────────────────────────────────────────────
const mocks = [
  { path: '/api/feature-flags', status: 200, response: { darkMode: true } },
  { path: '/api/user/me',        status: 200, response: { id: 'dev-user' } },
  { path: '/api/broken',         status: 503, response: { error: 'unavailable' } },
];

// ── Per-path extra headers ─────────────────────────────────────────
const extraHeaders = [
  { match: '/api/rates', headers: { 'X-Desk': 'rates-trading' } },
  { match: '/api/admin', headers: { 'X-Role-Override': 'admin' } },
];

module.exports = { sessionCookies, mocks, extraHeaders, TARGET };
```

-----

## 5. Mocked responses

Register mocks before the proxy. Express middleware runs in registration order — a mock that calls `res.json()` short-circuits the stack; the proxy beneath never fires.

```js
// In setupMiddlewares, before devServer.app.use('/api', apiProxy)
mocks.forEach(({ path, response, status = 200 }) => {
  devServer.app.use(path, (req, res) => {
    res.status(status).json(response);
  });
});
```

### Extensions

- **Method-specific:** check `req.method` inside the handler
- **Latency simulation:** wrap `res.json()` in `setTimeout`
- **Toggle via env:** wrap in `if (process.env.USE_MOCKS !== 'false')`
- **Stateful mocks:** handler closes over local state for sequences of responses

-----

## 6. Per-path extra headers

Inject additional headers on specific paths inside the `proxyReq` hook:

```js
const apiProxy = createProxyMiddleware({
  target: TARGET,
  changeOrigin: true,
  on: {
    proxyReq: (proxyReq, req) => {
      extraHeaders.forEach(({ match, headers }) => {
        if (req.url.startsWith(match)) {
          Object.entries(headers).forEach(([k, v]) => {
            proxyReq.setHeader(k, v);
          });
        }
      });
    },
  },
});
```

-----

## 7. Cookie bootstrap

Some backends require cookies set by a bootstrap endpoint. The proxy captures `Set-Cookie` headers from that endpoint and injects them into all subsequent API requests.

### Flow

1. Browser hits `/api/services/bootstrap/*`
1. `bootstrapProxy` forwards the request and captures `Set-Cookie` from the response
1. Cookies are stored in the proxy process (`sessionCookies` array)
1. `apiProxy` injects those cookies into every subsequent `/api` request

### Bootstrap proxy

```js
const bootstrapProxy = createProxyMiddleware({
  target: TARGET,
  changeOrigin: true,
  on: {
    proxyRes: (proxyRes) => {
      const setCookie = proxyRes.headers['set-cookie'];
      if (setCookie) {
        // Strip cookie attributes (Path, HttpOnly etc.), keep name=value only
        sessionCookies = setCookie.map(c => c.split(';')[0]);
        console.log('[proxy] Bootstrap cookies captured:', sessionCookies);
      }
    },
  },
});
```

### API proxy with cookie injection

```js
const apiProxy = createProxyMiddleware({
  target: TARGET,
  changeOrigin: true,
  on: {
    proxyReq: (proxyReq, req) => {
      // Extra headers
      extraHeaders.forEach(({ match, headers }) => {
        if (req.url.startsWith(match)) {
          Object.entries(headers).forEach(([k, v]) => proxyReq.setHeader(k, v));
        }
      });

      // Bootstrap cookies
      if (sessionCookies.length > 0) {
        const existing = proxyReq.getHeader('cookie') || '';
        const merged = [existing, ...sessionCookies].filter(Boolean).join('; ');
        proxyReq.setHeader('Cookie', merged);
      }
    },
  },
});
```

### Cookie expiry handling

Parse `Max-Age` from `Set-Cookie` and clear the store when it lapses:

```js
const maxAge = setCookie[0].match(/max-age=(\d+)/i)?.[1];
if (maxAge) {
  setTimeout(() => {
    sessionCookies = [];
    console.log('[proxy] Session cookies expired — re-bootstrap needed');
  }, parseInt(maxAge) * 1000);
}
```

### Multiple bootstrap sub-paths

If different sub-paths return different cookie sets, key by path:

```js
const sessionCookies = new Map();
// e.g. Map { '/api/services/bootstrap/auth' => ['sid=abc'] }
```

### Eager bootstrap on server start

Rather than waiting for the browser, fetch the bootstrap endpoint during `setupMiddlewares` so cookies are ready before the first request arrives:

```js
const fetch = require('node-fetch');

async function prefetchBootstrap() {
  const res = await fetch(`${TARGET}/api/services/bootstrap/auth`);
  const cookies = res.headers.raw()['set-cookie'] ?? [];
  sessionCookies = cookies.map(c => c.split(';')[0]);
  console.log('[proxy] Eagerly bootstrapped', sessionCookies.length, 'cookies');
}

// Call inside setupMiddlewares (fire-and-forget is fine for dev)
prefetchBootstrap().catch(console.error);
```

-----

## 8. Adding ADFS auth (extension point)

When ADFS authentication is wired in, the `proxyReq` hook on `apiProxy` injects both the Bearer token and the bootstrap cookies in one place:

```js
on: {
  proxyReq: async (proxyReq, req) => {
    // 1. ADFS Bearer token
    const token = await tokenManager.getToken();
    proxyReq.setHeader('Authorization', `Bearer ${token}`);

    // 2. Per-path extra headers
    extraHeaders.forEach(({ match, headers }) => {
      if (req.url.startsWith(match)) {
        Object.entries(headers).forEach(([k, v]) => proxyReq.setHeader(k, v));
      }
    });

    // 3. Bootstrap session cookies
    if (sessionCookies.length > 0) {
      const existing = proxyReq.getHeader('cookie') || '';
      const merged = [existing, ...sessionCookies].filter(Boolean).join('; ');
      proxyReq.setHeader('Cookie', merged);
    }
  },
},
```

> **HPM v3 fully awaits async `proxyReq` hooks.** The built-in webpack `proxy:` config does not — making this standalone approach essential for async token injection.
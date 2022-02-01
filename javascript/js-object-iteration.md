# Iterating and transforming objects

## Iterating

### The difference between `of` and `in`

The `of` operator is used in a `for .. of` loop to iterate over and `Iterable` object

```js
const phones = [
  { model: "iPhone X", color: "black" },
  { model: "Nokia old", color: "white" },
  { model: "Samsung", color: "blue" },
  { model: "Moto", color: "black" },
];

for (const { model, color } of phones) {
  console.log(`The ${model} is ${color}`);
}
```

The `in` operator iterates over the keys in an object. The key can then be used to retrieve the value from the object

```js
const phoneMap = {
  Apple: "iPhone X",
  Nokia: "8110",
  Samsung: "Galaxy",
  Motorola: "Moto",
};

for (const phoneMake in phoneMap) {
  console.log(`${phoneMake} makes the ${phoneMap[phoneMake]}`);
}
```
The `in` operator will only iterate enumerable properties of an object which means it does not walk the prototype chain.

### Other methods for enumerating an objects properties

The `Object` class has three static methods which return an `Array` of parts of the object

```js
const phoneMap = {
  Apple: "iPhone X",
  Nokia: "8110",
  Samsung: "Galaxy",
  Motorola: "Moto",
};
```

`Object.keys` returns the `keys` of the object and is very similar to the `in` operator

```js
for(const phoneKey of Object.keys(phoneMap)) {
    console.log(`${phoneKey} makes the ${phoneMap[phoneKey]}`);
};
```

`Object.values` returns just the values

```js
for(const model of Object.values(phoneMap)) {
    console.log(`Model ${model}`);
}
```

`Object.entries` returns an array of two item tuples where the tuple is the key and the value of each entry. This is the closest operator in JS that allows a JS object to behave like a Map object

```js
for(const [key, value] of Object.entries(phoneMap)) {
    console.log(`${key} makes the ${value}`);
}
```

## Transforming

Many tasks involve; mapping an object from one shape to another, filtering the data, grouping the data or aggregating.

### Filtering

```js
const allPhones = {
  Apple: { name: "iPhone X", price: 800 },
  Nokia: { name: "8110", price: 50 },
  Samsung: { name: "Galaxy", price: 200 },
  Motorola: { name: "Moto", price: 600 },
};
```

The combination of `Object.entries` and the `for .. of` loop can be used for filter:

```js
let affordablePhones = {};
for (const [key, value] of Object.entries(allPhones)) {
  if (value.price < 500) {
    affordablePhones = {
      ...affordablePhones,
      [key]: value,
    };
  }
}
```

Here the new object `affordablePhones` is mutable (the `let` keyword )as a new object is assigned on each iteration. To preserve the state from the last iteration the previous version is included by using the `spread` operator

The same result can also be achieved using the array function `filter` and `reduce`:

```js
const affordablePhones2 = Object.entries(allPhones)
  .filter(([key, value]) => value.price < 500)
  .reduce((acc, [key, value]) => ({ ...acc, [key]: value }), {});
```


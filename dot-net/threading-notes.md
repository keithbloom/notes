# Threading and Locking

## Monitor.Enter and Monitor.Exit (lock keyword)

- Ensures that only one thread can access a value at once
- Most common locking method in .net

```c#
class ThreadSafe
{
  static readonly object _locker = new object();
  static int _x;
 
  static void Increment() { lock (_locker) _x++; }
  static void Assign()    { lock (_locker) _x = 123; }
}
```


## Mutex 

- An application wide locking construct that be be used to ensure only one copy of the application is running
- The mutex must be released from the thread that created it

```c#
static void Main() 
{
    using(var mutex = new Mutex(false, "myid")) 
    {
        if(!mutex.WaitOne(TimeSpan.FromSeconds(3), false)) 
        {
            // Already running
            return;
        }
    }
}
```

## Semaphore

- Used to control how many threads can enter a resource at once
- If the limit is set to one then it is the same as using a lock without the need for a locking object

```c#
class Program
{
    static SemaphoreSlim sem = new SemaphoreSlim(3);
    static void Main(string[] args)
    {
        var tasks = new Task[5];
        for(int i = 0; i <= 4; i++)
        {
            tasks[i] = Task.Run(() =>
            {
                var id = Task.CurrentId;
                Console.WriteLine($"{id} wants to enter");
                sem.Wait();
                Console.WriteLine($"{id} is in");
                Thread.Sleep(1000 * (int)id);
                Console.WriteLine($"{id} is leaving");
                sem.Release();
            });
        }

        Thread.Sleep(500);
        Task.WaitAll(tasks);
    }

}
```



## Cancellation token

## Non blocking

### Memory barriers

### Interlocked

- Provides a set of methods allow values to be altered. Notably
    - Increment, Decrement, Add

`CompareExchange` is interesting. It takes three params; ref location, newValue, comparison. If location == comparison then newValue will be written to the location. It allows us to ensure that a value has not changed during our work by another thread

```c#
private int _balance;
void (int addValue) 
{
    var initialBalance = _balance; // Copy value at the start of the work
    // ... lots of work doing fun stuff
    var newBalance = initialBalance + addValue
    while(initialBalance != Interlocked.CompareExchange(ref _balance, newBalance, initialBalance));
    return newBalance;
}
```

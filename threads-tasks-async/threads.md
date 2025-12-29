## CPU, Thread and Thread Scheduler
- A thread is a basic unit of work that a CPU can execute. In general a application will always have atleast one thread (aka `main thread`, `UI thread`, etc.).
- Thread scheduler is used to load thread in the CPU. It is responsible to assign a `Thread` to `CPU`.
    - `Thread Scheduler` will use Time Slicing approach to execute multiple Threads on CPU.
    - `Thread Scheduler` is the part of Operating System and out of the Developer Control.
- There is no direct intreaction between `Application` and `CPU`. `CPU` intreacts with `Thread` via `Thread Scheduler`.

### Critical Section of code
A critical section of code is the part of a program where shared resources (like variables, files, or memory) are accessed or modified.
- Only one thread should execute this section at a time to avoid race conditions.
- If multiple threads enter the critical section simultaneously, data corruption or inconsistent results can occur.

### Threads Synchronization
Threre are multiple ways to do threads synchronization

### Exclusive Lock
- An exclusive lock means only one thread can enter a critical section of code at a time.
- Other threads attempting to acquire the lock are blocked until the lock is released.
- This prevents race conditions and ensures thread safety when working with shared data.

- Syntex of `lock` in c#
```
lock (lockObject)
{
    // Critical section: only one thread can execute this at a time
}
```
- lockObject must be a reference type (commonly private readonly object).
- The runtime uses Monitor.Enter and Monitor.Exit under the hood to manage the lock.

- Example

```
class Counter
{
    private int count = 0;
    private readonly object lockObj = new object();

    public void Increment()
    {
        lock (lockObj)
        {
            count++;
            Console.WriteLine($"Count: {count}");
        }
    }
}
```

### Monitor
- `Monitor.Enter` is part of the System.Threading.Monitor class.
- It provides exclusive locking for a block of code, just like `lock`.
- When a thread calls `Monitor.Enter(obj)`, it acquires a lock on `obj`.
- Other threads trying to enter with the same object are blocked until the lock is released.

- Example, 

```
object lockObj = new object();

Monitor.Enter(lockObj);
try
{
    // Critical section
    Console.WriteLine("Thread-safe work here");
}
finally
{
    // Always release the lock
    Monitor.Exit(lockObj);
}
```

#### Key Points
- You must always pair `Monitor.Enter` with `Monitor.Exit`.
- The `try/finally` ensures the lock is released even if an exception occurs.
- Forgetting `Monitor.Exit` can cause deadlocks.


- `TryEnter` lets you attempt to acquire a lock with a timeout. Useful when you don’t want threads to wait indefinitely.

- Example, 

```
object lockObj = new object();

if (Monitor.TryEnter(lockObj, 1000)) // wait up to 1 second
{
    try
    {
        Console.WriteLine("Lock acquired");
    }
    finally
    {
        Monitor.Exit(lockObj);
    }
}
else
{
    Console.WriteLine("Could not acquire lock");
}
```

- Use lock for most cases — it’s cleaner and safer.
- Use Monitor.Enter when you need advanced control:
  - Timeouts (TryEnter)
  - Signaling between threads (Wait, Pulse)
  - More complex synchronization patterns



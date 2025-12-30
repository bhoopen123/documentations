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

### Mutex
A `Mutex` is a synchronization primitive that can be used across multiple processes, while a `lock` (keyword) is limited to threads within the same process. `Mutex` is heavier and slower because it relies on the operating system kernel, whereas `lock` is faster and simpler since it’s just syntactic sugar over `Monitor`.

- A Mutex (short for Mutual Exclusion) is an object that ensures only one thread or process can access a resource at a time.
- Works across multiple processes, not just within a single application.
- Slower than lock because it involves kernel-level operations.

- Example
```
Mutex mutex = new Mutex();
mutex.WaitOne();   // Acquire the mutex
// Critical section
mutex.ReleaseMutex(); // Release the mutex
```

- Example 1: Basic Mutex in a Single Process
Even though lock is usually enough inside one process, you can still use a Mutex:

```csharp
using System;
using System.Threading;

class Program
{
    static Mutex mutex = new Mutex();

    static void Main()
    {
        for (int i = 0; i < 3; i++)
        {
            Thread t = new Thread(Worker);
            t.Start(i);
        }
    }

    static void Worker(object id)
    {
        Console.WriteLine($"Thread {id} waiting for Mutex...");
        mutex.WaitOne(); // Acquire
        Console.WriteLine($"Thread {id} entered critical section.");
        Thread.Sleep(1000); // Simulate work
        Console.WriteLine($"Thread {id} leaving critical section.");
        mutex.ReleaseMutex(); // Release
    }
}
```

Output: 
```text
Thread 0 waiting for Mutex...
Thread 0 entered critical section.
Thread 1 waiting for Mutex...
Thread 0 leaving critical section.
Thread 1 entered critical section.
```

👉 Only one thread at a time enters the critical section.

🧩 Example 2: Named Mutex Across Processes
This is where Mutex shines compared to lock. You can synchronize different applications.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        // Named Mutex (system-wide)
        using (Mutex mutex = new Mutex(false, "Global\\MyAppMutex"))
        {
            if (!mutex.WaitOne(0, false))
            {
                Console.WriteLine("Another instance is already running!");
                return;
            }

            Console.WriteLine("Running... Press Enter to exit.");
            Console.ReadLine();
        }
    }
}
```

Scenario:
- Run this program twice.
- The second instance will detect the first one and exit with the message:
"Another instance is already running!"
👉 This is a common technique to prevent multiple instances of an application.

🧩 Example 3: Timeout with Mutex
You can set a timeout when waiting for a Mutex.

```csharp
if (mutex.WaitOne(2000)) // Wait for 2 seconds
{
    try
    {
        Console.WriteLine("Acquired Mutex!");
        // Work here
    }
    finally
    {
        mutex.ReleaseMutex();
    }
}
else
{
    Console.WriteLine("Could not acquire Mutex within 2 seconds.");
}
```
👉 Useful when you don’t want threads to block indefinitely.

👉 If you forget to release, the Mutex stays locked, causing deadlocks.

⚖️ When to Use Mutex
- Use lock for thread synchronization inside one process (faster, simpler).
- Use Mutex when:
  - You need cross-process synchronization.
  - You want to prevent multiple instances of an app.
  - You need OS-level synchronization objects.


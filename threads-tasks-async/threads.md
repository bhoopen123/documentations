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

### Reader and Writer Lock
`ReaderWriterLockSlim` in C# is a thread synchronization technique that allows multiple threads to read concurrently while ensuring only one thread can write at a time on a shared resource. It improves performance compared to a simple lock when reads are frequent and writes are rare.

🔑 What is ReaderWriterLockSlim?
- Designed for multithreaded environments where shared resources are accessed by multiple readers and occasional writers.
- Key advantage: Multiple readers can access simultaneously, but writers get exclusive access.
- Supports UpgradeableReadLock: a thread can start with read access and later upgrade to write access without releasing the lock.

⚙️ Lock Modes

| Lock Mode | Purpose |
|---------- | -------- | 
| EnterReadLock() | Multiple threads can read concurrently. |
| EnterWriteLock() | Only one thread can write; blocks readers and other writers. |
 EnterUpgradeableReadLock() | Allows a thread to read, but later upgrade to write if needed. |
| ExitReadLock() / ExitWriteLock() | Releases the lock after use. |

📖 Example 1: Basic Reader/Writer

```csharp
using System;
using System.Threading;

class Program
{
    private static ReaderWriterLockSlim rwLock = new ReaderWriterLockSlim();
    private static string sharedData = "Initial Value";

    static void Reader()
    {
        rwLock.EnterReadLock();
        try
        {
            Console.WriteLine($"Reader Thread {Thread.CurrentThread.ManagedThreadId} read: {sharedData}");
        }
        finally
        {
            rwLock.ExitReadLock();
        }
    }

    static void Writer()
    {
        rwLock.EnterWriteLock();
        try
        {
            sharedData = $"Updated by Thread {Thread.CurrentThread.ManagedThreadId}";
            Console.WriteLine(sharedData);
        }
        finally
        {
            rwLock.ExitWriteLock();
        }
    }

    static void Main()
    {
        Thread t1 = new Thread(Reader);
        Thread t2 = new Thread(Writer);
        Thread t3 = new Thread(Reader);

        t1.Start();
        t2.Start();
        t3.Start();
    }
}
```

👉 Here, multiple readers can run simultaneously, but the writer blocks them until it finishes.

⚠️ Risks & Best Practices

- Always release locks in finally blocks to avoid deadlocks.

- Prefer `ReaderWriterLockSlim` over `ReaderWriterLock` (older class) because it’s faster and avoids common deadlock scenarios.

Use when reads dominate writes; otherwise, a simple lock may be more efficient.

✅ In short: Use ReaderWriterLockSlim when you have frequent reads and occasional writes to shared data. It boosts performance by allowing concurrent readers while still protecting against race conditions.

C# ReaderWriterLockSlim Class - GeeksforGeeks. https://www.geeksforgeeks.org/c-sharp/c-sharp-readerwriterlockslim-class/

System.Threading.ReaderWriterLockSlim class - .NET. https://learn.microsoft.com/en-us/dotnet/fundamentals/runtime-libraries/system-threading-readerwriterlockslim

C# ReaderWriterLockSlim - C# Tutorial. https://www.csharptutorial.net/csharp-concurrency/csharp-readerwriterlockslim/


### Semaphore / SemaphoreSlim
A `Semaphore` in C# is a thread synchronization primitive that controls access to a shared resource by limiting the number of threads that can enter a critical section at the same time. Unlike a `lock` (which allows only one thread), a semaphore can allow N threads concurrently.

- Maintains a count of how many threads can access a resource simultaneously.
- When the count reaches zero, additional threads are blocked until another thread releases the semaphore.
- Useful for resource pools (like database connections, file handles, or limited hardware resources).


#### Types of Semaphores
 - Semaphore : Can be shared across processes. 
 - SemaphoreSlim: Lightweight, faster, but limited to threads within the same process. 

```csharp
using System;
using System.Threading;

class Program
{
    // Allow max 3 threads at a time
    private static Semaphore semaphore = new Semaphore(3, 3);

    static void Worker(int id)
    {
        Console.WriteLine($"Thread {id} waiting...");
        semaphore.WaitOne(); // Acquire
        try
        {
            Console.WriteLine($"Thread {id} entered.");
            Thread.Sleep(2000); // Simulate work
            Console.WriteLine($"Thread {id} leaving.");
        }
        finally
        {
            semaphore.Release(); // Release
        }
    }

    static void Main()
    {
        for (int i = 1; i <= 6; i++)
        {
            new Thread(() => Worker(i)).Start();
        }
    }
}
```

👉 Output shows only 3 threads run at once, others wait until a slot is free.


Using SemaphoreSlim
```csharp
using System;
using System.Threading;

class Program
{
    private static SemaphoreSlim semaphoreSlim = new SemaphoreSlim(2); // allow 2 threads

    static async Task Worker(int id)
    {
        Console.WriteLine($"Worker {id} waiting...");
        await semaphoreSlim.WaitAsync(); // async acquire
        try
        {
            Console.WriteLine($"Worker {id} entered.");
            await Task.Delay(1000); // simulate async work
            Console.WriteLine($"Worker {id} leaving.");
        }
        finally
        {
            semaphoreSlim.Release();
        }
    }

    static async Task Main()
    {
        var tasks = Enumerable.Range(1, 5).Select(i => Worker(i));
        await Task.WhenAll(tasks);
    }
}
```

👉 SemaphoreSlim is ideal for async/await scenarios in modern C# applications.

⚠️ Best Practices
- Always release the semaphore in a finally block to avoid deadlocks.
- Use SemaphoreSlim for in-process scenarios (faster, supports async).
- Use Semaphore when you need cross-process synchronization.
- Don’t set the count too high—otherwise it defeats the purpose of limiting access.

✅ In short: Semaphores are perfect when you want to allow multiple threads (but not unlimited) to access a resource at the same time.
Real-world example are like controlling access to a database connection pool.

### Signaling
Signaling is a way for threads to communicate—one thread can pause until another thread signals it to continue. The most common signaling primitives are `AutoResetEvent` and `ManualResetEvent`. An `AutoResetEvent` automatically resets after releasing a single waiting thread, while a `ManualResetEvent` stays signaled until you manually reset it.

- Thread signaling lets one thread notify another that it can proceed.
- Implemented via WaitHandle-derived classes (AutoResetEvent, ManualResetEvent).
- Useful when you need to coordinate tasks across multiple threads.

#### AutoResetEvent
Behavior:
- Starts in either signaled (true) or non-signaled (false) state.
- When signaled, it releases exactly one waiting thread and then automatically resets to non-signaled.
- Other threads remain blocked until signaled again.

📖 Example: AutoResetEvent
```csharp
using System;
using System.Threading;

class Program
{
    static AutoResetEvent autoEvent = new AutoResetEvent(false);

    static void Worker(int id)
    {
        Console.WriteLine($"Worker {id} waiting...");
        autoEvent.WaitOne(); // Wait for signal
        Console.WriteLine($"Worker {id} proceeding!");
    }

    static void Main()
    {
        for (int i = 1; i <= 3; i++)
        {
            new Thread(() => Worker(i)).Start();
        }

        Thread.Sleep(1000);
        Console.WriteLine("Main thread signaling...");
        autoEvent.Set(); // Releases one worker
        Thread.Sleep(1000);
        autoEvent.Set(); // Releases another worker
        Thread.Sleep(1000);
        autoEvent.Set(); // Releases the last worker
    }
}
```

👉 Output shows workers waiting, then each one proceeds one at a time when signaled.

#### ManualResetEvent (for comparison)
- Unlike `AutoResetEvent`, `ManualResetEvent` stays signaled until you call `Reset()`.
- Multiple threads can proceed once signaled.

```csharp
ManualResetEvent manualEvent = new ManualResetEvent(false);

// Signal all waiting threads
manualEvent.Set();

// Reset to block again
manualEvent.Reset();
```

📌 Real-World Use Cases

AutoResetEvent:
- Producer-consumer scenarios (one item wakes one consumer).
- Sequential task execution.

ManualResetEvent:
- Broadcasting a signal to multiple threads (e.g., "start processing now").

⚠️ Best Practices
- Always call WaitOne() inside a thread that should pause until signaled.
- Use Set() to signal, and Reset() (for ManualResetEvent) to block again.
- Prefer SemaphoreSlim or Task-based coordination for modern async code, but events are still useful for low-level thread control.

## Thread Affinity

In C#, thread affinity refers to binding a thread (or process) to specific CPU cores so that it always executes on those cores. This is done using processor affinity masks, which control which CPUs the operating system scheduler can assign to a thread.

## Thread Safe

In multi threading computer programming, a function or data structure or a class is thread safe when it can be used concurrently by multiple threads without causing race conditions, unexpected behaviors, or data corruptions.

## Debugging Multi-threaded program in Visual Studio
- Run the application in `Debug` mode.
- Pause the program, (or use debug breaks).
- Once you pause the program all the threads will get paused.
- Use `Threads` window (Debug -> windows -> Threads) and `Parallel Stack` window (Debug -> windows -> Parallel Stacks).
- In the `Threads` window, we can freeze a thread to change the thread execution sequence (Freeze and Continue). and then release the thread (Play and Continue).

## Thread States
ThreadState Enum (System.Threading)

| State | Meaning |
|-------- | -------| 
| Unstarted |Thread created but not started  | 
| Running | Thread is executing  | 
| WaitSleepJoin | Thread is blocked (waiting, sleeping, or joining another thread) | 
| Suspended | Thread execution suspended (legacy, unsafe) | 
| AbortRequested | Abort has been requested  | 
| Aborted | Thread has been aborted  | 
| Stopped | Thread has finished execution  | 

Important Notes

- Modern .NET discourages `Suspend()` and `Abort()` because they are unsafe and can leave shared resources in inconsistent states.
- Instead, use cancellation tokens (`CancellationTokenSource`) for cooperative cancellation.
- Thread states are mainly useful for diagnostics and debugging, not for controlling thread execution directly.
- You can't see the Thread State in the Debug mode (neither using `Threads` window or `Intermediate` window). It can be print in the logs.

## Make Thread wait

1. `Thread.Sleep(int millisecondsTimeout)`
- Behavior: Suspends the current thread for the specified time.
- Mechanism: The OS scheduler removes the thread from the CPU and puts it into a waiting state.
- Pros:
    - Frees CPU resources during the sleep.
    - Simple and predictable.
- Cons:
    - Context switch overhead (thread goes to sleep, then wakes up).
    - Not precise for very short waits (resolution depends on OS timer).
- Use case: Long waits (hundreds of ms or seconds) where CPU efficiency matters.

```csharp
Thread.Sleep(1000); // Sleep for 1 second
```

🔄 2. `Thread.SpinWait(int iterations)`
- Behavior: Busy-waits by repeatedly executing a small loop for the given number of iterations.
- Mechanism: The thread does not yield; it keeps the CPU busy spinning.
- Pros:
   - Extremely fast for very short waits (nanoseconds to microseconds).
   - Avoids context switch overhead.
- Cons:
   - Wastes CPU cycles while spinning.
   - Not suitable for longer waits.
- Use case: Low-level synchronization, waiting for a condition that will be true almost immediately.

```csharp
Thread.SpinWait(1000); // Spins for ~1000 iterations
```

⏱️ 3. `SpinWait.SpinUntil(Func<bool> condition, int millisecondsTimeout)`
- Behavior: Spins until a condition becomes true or timeout expires.
- Mechanism: Internally uses a mix of spinning and yielding (adaptive strategy).
- Pros:
   - More flexible: waits for a condition, not just time.
   - Adaptive: starts with busy spinning, then yields/sleeps if condition takes longer.
- Cons:
   - Still wastes CPU if condition takes long.
   - Timeout precision depends on OS.
- Use case: Waiting for a flag/condition that is expected to be set quickly.

```csharp
bool success = SpinWait.SpinUntil(() => flag == true, 2000);
```

⚖️ Comparison Table
| Method | Type of Wait | CPU Usage | Precision | Best For |
|--------|--------------|-----------|-----------|----------|
| Thread.Sleep | Blocking  | Low  | Coarse | Long waits, freeing CPU | 
| Thread.SpinWait | Busy-spin  | High | Very fine | Ultra-short waits, avoiding context switch | 
| SpinWait.SpinUntil | Conditional | Adaptive | Medium | Waiting for quick condition with timeout | 

#### Summary
- Use `Thread.Sleep` when you want to pause without hogging CPU.
- Use `Thread.SpinWait` for tiny waits where context switch overhead is worse than spinning.
- Use `SpinWait.SpinUntil` when you need to wait for a condition but expect it to be satisfied quickly.

## Thread Result
There is no build in way to return a result from a Thread. One way is to do that is using a Shared variable.


## Cancelling a Thread
Cancelling a thread in C# is a bit nuanced because threads don’t have a built-in cancellation mechanism. Unlike `Tasks` (which support `CancellationToken`), a Thread must be cooperatively stopped by the code running inside it.

### Why You Can’t Just “Kill” a Thread
- Methods like `Thread.Abort()` existed in older .NET, but they are obsolete and unsafe (they throw exceptions inside the thread, leaving shared state inconsistent).
- Modern .NET encourages cooperative cancellation: the thread checks a flag or token and exits gracefully.

### Common Approaches to Cancel a Thread

#### 1. Using a Shared Flag
- You define a `volatile` boolean flag that the thread checks periodically.
- When you set the flag, the thread exits its loop.

```csharp
using System;
using System.Threading;

class Program
{
    static volatile bool _cancel = false;

    static void Worker()
    {
        while (!_cancel)
        {
            Console.WriteLine("Working...");
            Thread.Sleep(500); // simulate work
        }
        Console.WriteLine("Thread cancelled gracefully.");
    }

    static void Main()
    {
        Thread t = new Thread(Worker);
        t.Start();

        Thread.Sleep(2000); // let it run for a while
        _cancel = true;     // request cancellation
        t.Join();           // wait for thread to finish
    }
}
```

#### 2. Using CancellationToken with Tasks (Preferred in Modern .NET)
Even though Thread doesn’t support tokens directly, you can use Task instead, which is more flexible.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        var cts = new CancellationTokenSource();

        Task t = Task.Run(() =>
        {
            while (!cts.Token.IsCancellationRequested)
            {
                Console.WriteLine("Working...");
                Thread.Sleep(500);
            }
            Console.WriteLine("Task cancelled gracefully.");
        }, cts.Token);

        await Task.Delay(2000);
        cts.Cancel(); // request cancellation
        await t;      // wait for task to finish
    }
}
```

#### 3. Using ManualResetEvent or AutoResetEvent
You can signal a thread to stop using synchronization primitives.

```csharp
using System;
using System.Threading;

class Program
{
    static ManualResetEvent stopSignal = new ManualResetEvent(false);

    static void Worker()
    {
        while (!stopSignal.WaitOne(0))
        {
            Console.WriteLine("Working...");
            Thread.Sleep(500);
        }
        Console.WriteLine("Thread stopped via signal.");
    }

    static void Main()
    {
        Thread t = new Thread(Worker);
        t.Start();

        Thread.Sleep(2000);
        stopSignal.Set(); // signal cancellation
        t.Join();
    }
}
```

#### Comparison of Approaches
| Approach  | Safe? | Ease of Use | Modern Recommendation | 
|-----------|-------|-------------|-----------------------|
| Thread.Abort() | No  | Easy | Deprecated, unsafe  | 
| volatile | Yes | Simple | Good for basic threads  | 
| CancellationToken | Yes | Very easy  | Best practice in modern .NET | 
| ManualResetEvent | Yes | Flexible | Good for signaling | 

👉 Summary:
- Never use `Thread.Abort`.
- For raw threads, use a shared flag or reset event.
- For modern apps, prefer Tasks with CancellationToken.

## ThreadPool

- The ThreadPool is a pool of worker threads managed by the CLR (Common Language Runtime).
- Instead of creating and destroying threads manually, you can queue work items to the pool.
- The runtime reuses threads, reducing overhead and improving scalability.
- It’s ideal for short-lived, lightweight tasks.

### Key Features
- Threads are background threads (they don’t keep the process alive).
- Managed by the CLR: you don’t control their lifecycle directly.
- Optimized for parallel execution of small tasks.
- Supports queuing work items, timers, and async I/O.

### Example: Queueing Work

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        // Queue a task to the ThreadPool
        ThreadPool.QueueUserWorkItem(Work, "Task 1");
        ThreadPool.QueueUserWorkItem(Work, "Task 2");

        Console.WriteLine("Main thread continues...");
        Thread.Sleep(2000); // Give time for tasks to finish
    }

    static void Work(object state)
    {
        Console.WriteLine($"Thread {Thread.CurrentThread.ManagedThreadId} is processing {state}");
        Thread.Sleep(1000); // simulate work
        Console.WriteLine($"{state} completed.");
    }
}
```

### Example: ThreadPool with Timer
ThreadPool also powers timers.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        Timer timer = new Timer(Callback, null, 0, 1000); // run every 1 sec
        Console.WriteLine("Press Enter to exit...");
        Console.ReadLine();
    }

    static void Callback(object state)
    {
        Console.WriteLine($"Timer callback on thread {Thread.CurrentThread.ManagedThreadId}");
    }
}
```

#### Comparison: Thread vs ThreadPool
| Aspect | Thread | ThreadPool |
|--------|--------|------------| 
|Creation| Manual (new Thread) | Automatic, managed by CLR | 
|Lifecycle| You control start/stop  | CLR manages reuse | 
|Overhead| Higher (new thread each time) | Lower (threads reused) | 
|Best for| Long-running, dedicated tasks  | Short-lived, lightweight tasks  | 
|Background/Foreground| Can be either  | Always background | 

#### Summary
- Use `ThreadPool` when you need to run many small tasks efficiently.
- Avoid it for long-running or blocking tasks (dedicated Thread or Task is better).
- In modern .NET, Task.Run internally uses the ThreadPool, so you often don’t need to call it directly.

## Exception Handling in Threads

Exception handling in C# threads is a bit different from normal synchronous code because exceptions thrown inside a thread don’t automatically propagate back to the thread that started it. You need to handle them inside the thread itself.

### 1. Handle Exceptions Inside the Thread
The most common way is to wrap the thread’s work in a try-catch.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        Thread t = new Thread(() =>
        {
            try
            {
                Console.WriteLine("Thread started...");
                throw new InvalidOperationException("Something went wrong!");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception caught in thread: {ex.Message}");
            }
        });

        t.Start();
        t.Join(); // Wait for thread to finish
        Console.WriteLine("Main thread continues...");
    }
}
```
👉 Here, the exception is caught inside the thread itself. If you don’t catch it, the thread will terminate abruptly.

### 2. Communicate Exceptions Back to Main Thread
Since exceptions don’t bubble up automatically, you can store them and rethrow in the main thread.

```csharp
Exception threadException = null;

Thread t = new Thread(() =>
{
    try
    {
        throw new ApplicationException("Thread failed!");
    }
    catch (Exception ex)
    {
        threadException = ex; // capture exception
    }
});

t.Start();
t.Join();

if (threadException != null)
{
    Console.WriteLine($"Exception from thread: {threadException.Message}");
    // Optionally rethrow: throw threadException;
}
```

### 3. Using Task Instead of Raw Threads (Preferred)
Tasks integrate with async/await and propagate exceptions automatically.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        try
        {
            await Task.Run(() =>
            {
                Console.WriteLine("Task started...");
                throw new InvalidOperationException("Task error!");
            });
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception caught in Main: {ex.Message}");
        }
    }
}
```
👉 With Task, you don’t need to manually capture exceptions—they flow naturally to the caller.

### 4. Global Exception Handling
For unhandled exceptions in threads:
- Use `AppDomain.CurrentDomain.UnhandledException` for non-UI threads.
- Use `Application.ThreadException` for Windows Forms UI threads.

```csharp
AppDomain.CurrentDomain.UnhandledException += (sender, e) =>
{
    Console.WriteLine($"Unhandled exception: {((Exception)e.ExceptionObject).Message}");
};
```

### Comparison
| Approach | Exception Handling Style | Best Use Case |
|----------|--------------------------|---------------| 
| `try-catch` inside thread | Local handling | Simple background threads | 
| Capture exception in variable | Manual propagation | Coordinating with main thread | 
| `Task` with `async/await` | Automatic propagation | Modern apps, async workflow | 
| Global handlers `UnhandledException` | Catch-all safety net | Logging, last-resort handling | 

### Summary:
- Always wrap thread code in try-catch.
- If you need the main thread to know about exceptions, capture and propagate them manually.
- Prefer Task over raw Thread for modern apps—exception handling is much cleaner.


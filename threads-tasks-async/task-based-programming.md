## Multithreading Vs Asynchronous Programming

Multi-threaded programming emphasis the divide and conquer aspect. So the performance is going to be improved greatly, whereas asynchronous programming emphasizes on offload the long running task.

Multi-threaded programming is often used for CPU bound operations, and the operation that will use a lot of CPU resources to calculate, for example, games, whereas asynchronous programming is particularly used for IO bound operations. Mostly it is from a remote server, a remote database. 

#### Comparison Table
| Aspect | Multithreading | Asynchronous Programming | 
|--------|----------------|--------------------------|
| Execution Model | Runs tasks in parallel across multiple threads | Runs tasks non-blocking, often on a single thread | 
| Thread Usage | Explicitly creates and manages multiple threads | Usually does not create new threads; uses event loops and continuations | 
| Best For | CPU-bound tasks (e.g., heavy computation, simulations) | I/O-bound tasks (e.g., network requests, file reads/writes | 
| Complexity | Requires synchronization (locks, semaphores) to avoid race conditions | Easier to manage; avoids many synchronization issues | 
| Performance Impact | Can fully utilize multiple cores, but overhead of context switching is high | Lower overhead; scales well for thousands of concurrent I/O operations | 
| Example in .NET | `Thread`, `Task.Run`, `Parallel.For` | `async/await`, `Task`, `ValueTask` | 
| Risk | Deadlocks, race conditions, thread starvation | Callback hell (if poorly structured), harder debugging | 

#### Summary
- Use multithreading when you need to maximize CPU usage for parallel computations.
- Use asynchronous programming when dealing with I/O operations where waiting is common, to keep applications responsive.

## Thread Vs Task

- A Task is a Promise that we are going to get a task done sometime in future when exactly we don't know. That is the aspect of asynchronous programming. Whereas a thread is a basic programming unit for running something in a CPU.
- A Task most of the time uses a thread to do something but doesn't have to. A Task provides higher level of abstraction that provides many benifits out of the box, whereas a thread is a basic programming unit.

#### Benifits of using Task instead of Thread
- Task uses ThreadPool by default
- Returning values
- Easy Continuation
- Better exception handling
- Async/Await: makes writing asynchronous code feels like writing synchronous code
- Async/Await: Wasy Context management (Synchronization context with async and await keyword) 

## Task : Wait(), WaitAll() and Result

### `Task.Wait()`
#### Definition:
 Blocks the calling thread until the task completes.
#### Usage:
```csharp
Task t = Task.Run(() => DoWork());
t.Wait(); // blocks until DoWork() finishes
```
#### Notes:
- Synchronous blocking.
- If the task throws an exception, Wait() will wrap it in an `AggregateException`.

### `Task.WaitAll()`
#### Definition:
 Blocks the calling thread until all tasks in an array/list complete.
#### Usage:

```csharp
Task t1 = Task.Run(() => DoWork1());
Task t2 = Task.Run(() => DoWork2());
Task.WaitAll(t1, t2); // waits until both finish
```
#### Notes:
- Useful when you want multiple tasks to finish before proceeding.
- Exceptions from multiple tasks are aggregated into a single AggregateException.

### `Task.Result`
#### Definition: 
Gets the result of a Task<T> once it completes.

#### Usage:

```csharp
Task<int> t = Task.Run(() => Calculate());
int result = t.Result; // blocks until Calculate() returns
```
#### Notes:
- Also blocks the calling thread until the task completes.
- If the task faults, accessing Result throws an AggregateException.
- Commonly used with Task<T> when you need the return value.

### Comparison Table
| Feature | `Wait()` | `WaitAll()` | `Result` | 
|-------- |----------|-------------|--------- | 
|Blocking| Yes (single task) | Yes (multiple tasks) | Yes (single task) | 
| Return Value | None | None | Returns T from Task<T> | 
|Exceptions  | AggregateException | AggregateException (all tasks) | AggregateException | 
|Use Case  | Ensure one task finishes | Ensure all tasks finish | Get computed result from a task  | 


### Practical Guidance
- Prefer `await` in modern async code instead of `Wait()` or `Result`, because blocking can cause deadlocks (especially in UI apps like WinForms/WPF).
- Use `Wait()` or `Result` only in console apps or scenarios where blocking is acceptable.
- Use `WaitAll()` when coordinating multiple tasks synchronously, but in `async` code prefer `await Task.WhenAll()`.

## Task : ContinueWith, WhenAll, WhenAny

### ContinueWith
- Chains a continuation task that runs after the original task completes.

```csharp
Task t = Task.Run(() => DoWork())
             .ContinueWith(prev => Console.WriteLine("Work finished!"));
```
#### Notes:
- Continuation runs regardless of success/failure unless you specify conditions (`OnlyOnRanToCompletion`, `OnlyOnFaulted`, etc.).
- Useful for sequential workflows where one task depends on another.

### Task.WhenAll
- Returns a task that completes when all supplied tasks finish.

```csharp
Task t1 = Task.Run(() => DoWork1());
Task t2 = Task.Run(() => DoWork2());
await Task.WhenAll(t1, t2); // async-friendly
```
#### Notes:
- Non-blocking (when awaited).
- If any task fails, the resulting task is faulted with an `AggregateException`.
- Great for parallel execution when you need all results before proceeding.

### Task.WhenAny
- Returns a task that completes when any one of the supplied tasks finishes.

```csharp
Task t1 = Task.Run(() => DoWork1());
Task t2 = Task.Run(() => DoWork2());
Task finished = await Task.WhenAny(t1, t2);
Console.WriteLine("First task done!");
```
#### Notes:
- Lets you react to the first completed task.
- Useful for timeouts, race conditions, or picking the fastest source.

### Comparison Table
|Feature  | `ContinueWith` | `WhenAll` | `WhenAny` | 
| -------  |-----------  |-------  |-------  | 
|Completion  |After one task finishes  |After all tasks finish  | After any one task finishes | 
| Return Value | New continuation task |Task representing all tasks  | Task representing first finished | 
| Error Handling |Can filter by status (success/fault)  | AggregateException if any fail | Exception only if chosen task fails | 
| Use Case |Sequential chaining  |Parallel aggregation  | Race/first-response scenarios | 



### Practical Guidance
- Use ContinueWith for chaining dependent steps (but prefer await for readability).
- Use WhenAll when you need all results (e.g., fetching data from multiple APIs).
- Use WhenAny when you only care about the first response (e.g., fastest mirror server).

- Demo Program

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        // --- ContinueWith Demo ---
        Task t1 = Task.Run(() =>
        {
            Console.WriteLine("Task 1: Doing work...");
            Task.Delay(1000).Wait();
        })
        .ContinueWith(prev =>
        {
            Console.WriteLine("Task 1 continuation: Work finished!");
        });

        // --- WhenAll Demo ---
        Task t2 = Task.Run(() =>
        {
            Console.WriteLine("Task 2: Doing work...");
            Task.Delay(1500).Wait();
            return 42;
        });

        Task t3 = Task.Run(() =>
        {
            Console.WriteLine("Task 3: Doing work...");
            Task.Delay(2000).Wait();
            return 99;
        });

        Task.WhenAll(t2, t3).ContinueWith(prev =>
        {
            Console.WriteLine($"WhenAll: Results = {t2.Result}, {t3.Result}");
        });

        // --- WhenAny Demo ---
        Task t4 = Task.Run(() =>
        {
            Task.Delay(1200).Wait();
            Console.WriteLine("Task 4 finished first!");
            return "Result from Task 4";
        });

        Task t5 = Task.Run(() =>
        {
            Task.Delay(2500).Wait();
            Console.WriteLine("Task 5 finished later!");
            return "Result from Task 5";
        });

        Task.WhenAny(t4, t5).ContinueWith(prev =>
        {
            Console.WriteLine($"WhenAny: First result = {prev.Result.Result}");
        });

        // Keep console alive until all tasks finish
        Task.WaitAll(t1, t2, t3, t4, t5);
        Console.WriteLine("All done!");
    }
}
```

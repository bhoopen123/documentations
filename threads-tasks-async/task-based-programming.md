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
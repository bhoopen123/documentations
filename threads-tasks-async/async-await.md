## Async - Await

- Everything after `await` keyword is considered a continuation. When program runs to the await keyword, the calling thread is immediately released, so it is free to do any other thing.

- Returns a value

- Throws exceptions of the task if there is any.

- Manages the synchronization context inside, for example, a resource which is created by UI thread can be modified by another thread without any error (using `async` and `await`).

### basic notes
- You can't have Concurrency without Asynchrony but you can have Asynchrony without Concurrency.

- `lock(this)` is equivalent to creating a `public` object inside the class and using this public object to lock on the internal implementation excution. So anyone having object of class can also access the lock object which should be private. 

## Parallel library
The Parallel library in C# (part of the Task Parallel Library in `System.Threading.Tasks`) provides high-level methods like `Parallel.For`, `Parallel.ForEach`, and `Parallel.Invoke` to run tasks concurrently, making CPU-bound operations faster and easier to manage. It abstracts away thread creation and scheduling, letting you focus on the logic instead of low-level threading details.

### Key Features:
- Runs loops concurrently (`Parallel.For`, `Parallel.ForEach`).
- Executes multiple actions simultaneously (`Parallel.Invoke`).
- Provides cancellation and exception handling support.
- Automatically manages thread pool usage and load balancing.

### Examples

1. Parallel.For
Runs iterations of a loop in parallel.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Parallel.For(0, 10, i =>
        {
            Console.WriteLine($"Task {Task.CurrentId} processing iteration {i}");
        });
    }
}
```
- Output: Iterations run concurrently, so order is not guaranteed.
- Use Case: CPU-intensive loops (e.g., mathematical computations).

2. Parallel.ForEach
Processes items in a collection concurrently.

```csharp
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        var numbers = new List<int> { 1, 2, 3, 4, 5 };

        Parallel.ForEach(numbers, n =>
        {
            Console.WriteLine($"Square of {n} = {n * n}");
        });
    }
}
```
- Output: Each item is processed in parallel.
- Use Case: Data processing across large collections.

3. Parallel.Invoke
Runs multiple independent actions simultaneously.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Parallel.Invoke(
            () => Console.WriteLine("Task A running"),
            () => Console.WriteLine("Task B running"),
            () => Console.WriteLine("Task C running")
        );
    }
}
```
- Output: All tasks execute concurrently.
- Use Case: Running independent methods (e.g., logging, calculations, file operations).

### Comparison Table
| Method | Purpose | Example Use Case | 
|--------|---------|------------------|
| `Parallel.For` | Parallel loop over index range | Image processing, simulations | 
| `Parallel.ForEach` | Parallel loop over collections  | Processing large datasets  | 
| `Parallel.Invoke` | Run multiple actions concurrently | Independent tasks like logging | 

### Best Practices
- Use for CPU-bound tasks (e.g., calculations, transformations).
- Avoid for I/O-bound tasks (use async/await instead).
- Handle exceptions with `AggregateException`.
- Use `ParallelOptions` to control degree of parallelism and cancellation.









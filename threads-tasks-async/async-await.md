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

### Exception Handling
When you run code with `Parallel.For`, `Parallel.ForEach`, or `Parallel.Invoke`, multiple tasks may throw exceptions concurrently. Instead of losing them or handling them one by one, the Parallel library wraps all exceptions into an `AggregateException` so you can process them together.

#### How Exceptions Work in Parallel
- Each parallel task runs independently.
- If one or more tasks throw exceptions, they are collected.
- At the end of execution, the Parallel method throws a single `AggregateException`.
- You can inspect or flatten this exception to see all inner exceptions.

#### Example: Handling Exceptions in Parallel.For
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        try
        {
            Parallel.For(0, 10, i =>
            {
                if (i == 5)
                    throw new InvalidOperationException($"Error at iteration {i}");

                Console.WriteLine($"Iteration {i}");
            });
        }
        catch (AggregateException ex)
        {
            foreach (var inner in ex.InnerExceptions)
            {
                Console.WriteLine($"Caught exception: {inner.Message}");
            }
        }
    }
}
```

- Iteration 5 throws an exception.
- The Parallel library collects it.
- At the end, it throws an `AggregateException`.
- You loop through InnerExceptions to see details.

#### Example: Multiple Exceptions in Parallel.Invoke

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        try
        {
            Parallel.Invoke(
                () => { throw new ArgumentNullException("Task A failed"); },
                () => { throw new InvalidOperationException("Task B failed"); },
                () => Console.WriteLine("Task C succeeded")
            );
        }
        catch (AggregateException ex)
        {
            ex.Handle(inner =>
            {
                Console.WriteLine($"Handled: {inner.GetType()} - {inner.Message}");
                return true; // mark as handled
            });
        }
    }
}
```
- Both Task A and Task B throw exceptions.
- They are wrapped into a single `AggregateException`.
- Handle lets you process each exception and decide if it’s handled.

#### Best Practices
- Always catch `AggregateException` when using Parallel methods.
- Use `.Flatten()` if exceptions are nested.
- Use `.Handle()` to filter or selectively process exceptions.
- Remember: if you don’t handle them, the exception will propagate and crash your program.

#### Quick Comparison
| Scenario | Exception Type Raised | 
|----------|-----------------------|
| Single task throws | AggregateException with 1 inner exception| 
| Multiple tasks throw | AggregateException with multiple inner exceptions| 
| Nested parallel calls | Nested `AggregateException` (use `.Flatten()`) | 

### Break and Stop
`Parallel.For` executes iterations of a loop in parallel across multiple threads. Sometimes, you want to exit early from the loop. That’s where `Break` and `Stop` come in.

#### Difference Between Break and Stop
| Feature | Break() | Stop() | 
|---------|---------|--------|
|Purpose  | Signals that no iterations after the current index should start. | Signals that all iterations should stop, regardless of index. | 
| Guarantee |Iterations before the current index may still run.  | No guarantee about order; all iterations may stop immediately. | 
| Use Case | When loop is ordered and you want to stop at a certain point (like finding a minimum). | When you want to cancel everything as soon as a condition is met. | 
| Performance | Allows already-started iterations < current index to finish |Cancels all iterations aggressively. | 



#### Example: Using `Break`
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Parallel.For(0, 20, (i, state) =>
        {
            Console.WriteLine($"Iteration {i}");

            if (i == 10)
            {
                Console.WriteLine("Break called at iteration " + i);
                state.Break();
            }
        });

        Console.WriteLine("Loop completed.");
    }
}
```

#### Explanation
- When i == 10, state.Break() is called.
- Iterations greater than 10 will not start.
- Iterations less than 10 may still continue if already running.

#### Example: Using `Stop`
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Parallel.For(0, 20, (i, state) =>
        {
            Console.WriteLine($"Iteration {i}");

            if (i == 10)
            {
                Console.WriteLine("Stop called at iteration " + i);
                state.Stop();
            }
        });

        Console.WriteLine("Loop completed.");
    }
}
```

#### Explanation
- When i == 10, state.Stop() is called.
- All iterations stop immediately, regardless of index.
- Even iterations less than 10 may be canceled if not yet started.

#### Key Takeaways
- Use Break when the loop is ordered and you want to stop at a certain index.
- Use Stop when you want to cancel everything as soon as a condition is met.
- Both are cooperative signals; threads already executing may finish their work.







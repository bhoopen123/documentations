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

### What is ParallelLoopResult?
- When you run a `Parallel.For` or `Parallel.ForEach`, the method returns a `ParallelLoopResult` object.
- This object tells you how the loop ended:
    - Did it run to completion?
    - Did it stop early because of Break or Stop?
    - If Break was used, at which iteration did it happen?

#### Properties of ParallelLoopResult
| Property | Type | Meaning |
|----------|------|---------|
| `IsCompleted` | `bool` | true if the loop ran all iterations without Break or Stop. | 
| `LowestBreakIteration` | `long?` (nullable) | The smallest iteration index at which Break was called. null if no Break occurred. | 

⚠️ Note: LowestBreakIteration is only relevant for Break, not Stop.

#### Example: Using Break and Checking Result
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        ParallelLoopResult result = Parallel.For(0, 20, (i, state) =>
        {
            Console.WriteLine($"Iteration {i}");

            if (i == 10)
            {
                Console.WriteLine("Break called at iteration " + i);
                state.Break();
            }
        });

        Console.WriteLine($"IsCompleted: {result.IsCompleted}");
        Console.WriteLine($"LowestBreakIteration: {result.LowestBreakIteration}");
    }
}
```

#### Output (sample)

```
Iteration 0
Iteration 1
...
Iteration 10
Break called at iteration 10
IsCompleted: False
LowestBreakIteration: 10
```


#### Example: Using Stop
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        ParallelLoopResult result = Parallel.For(0, 20, (i, state) =>
        {
            Console.WriteLine($"Iteration {i}");

            if (i == 10)
            {
                Console.WriteLine("Stop called at iteration " + i);
                state.Stop();
            }
        });

        Console.WriteLine($"IsCompleted: {result.IsCompleted}");
        Console.WriteLine($"LowestBreakIteration: {result.LowestBreakIteration}");
    }
}
```

#### Output (sample)
```
Iteration 0
Iteration 1
...
Iteration 10
Stop called at iteration 10
IsCompleted: False
LowestBreakIteration:
```

👉 Notice: LowestBreakIteration is null because Stop was used.

#### Key Takeaways
- `ParallelLoopResult` is your status report after a parallel loop.
- Use `IsCompleted` to check if all iterations ran.
- Use `LowestBreakIteration` to know where the loop stopped when Break was used.
- With Stop, `LowestBreakIteration` is always null.

### Cancellation in Parallel Loop

- It doesn’t have a built-in `CancellationToken` parameter. Instead, you use a `ParallelOptions` object, which allows you to pass a CancellationToken.
- When cancellation is requested, the loop throws an `OperationCanceledException`.

#### Example

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        var cts = new CancellationTokenSource();
        var options = new ParallelOptions
        {
            CancellationToken = cts.Token,
            MaxDegreeOfParallelism = Environment.ProcessorCount
        };

        try
        {
            Parallel.For(0, 1000, options, i =>
            {
                Console.WriteLine($"Processing {i}");

                // Simulate work
                Thread.Sleep(50);

                // Cancel if condition met
                if (i == 50)
                {
                    cts.Cancel(); // request cancellation
                }

                // Respect cancellation
                options.CancellationToken.ThrowIfCancellationRequested();
            });
        }
        catch (OperationCanceledException)
        {
            Console.WriteLine("Loop was cancelled!");
        }
    }
}
```

#### How It Works
- `ParallelOptions` carries the `CancellationToken`.
- Inside the loop body, you can call `ThrowIfCancellationRequested()` to stop gracefully.
- Once cancellation is triggered, all running iterations will attempt to stop, and the loop throws `OperationCanceledException`.

#### Important Notes
- Not all iterations stop immediately: Some may still finish if they were already running.
- Always wrap `Parallel.For` in a try/catch for `OperationCanceledException`.
- You can combine cancellation with timeouts or user input for more control.




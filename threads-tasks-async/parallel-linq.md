# PLINQ
Parallel LINQ (PLINQ) lets you run LINQ queries across multiple threads to leverage multi-core CPUs.

## Key Concepts in PLINQ
### `AsParallel()`
- **Purpose:** Converts a standard LINQ query into a parallel query.
- **Usage:** Call it on any `IEnumerable<T>` source.
- **Effect:** The query is partitioned and executed concurrently across multiple threads.

#### Example:
```csharp
var numbers = Enumerable.Range(1, 1000000);
var evenNumbers = numbers.AsParallel()
                         .Where(n => n % 2 == 0)
                         .ToList();

```
- Here, filtering (`Where`) runs in parallel.


### `ForAll()`
- **Purpose:** Executes an action on each element of the parallel query result in parallel.
- **Difference from foreach:** `foreach` consumes results sequentially, while `ForAll()` processes them concurrently.

#### Example:
```csharp
var numbers = Enumerable.Range(1, 100);
numbers.AsParallel()
       .Where(n => n % 2 == 0)
       .ForAll(n => Console.WriteLine(n));
```

- Output may be unordered, since multiple threads write simultaneously.


### Comparison Table

| **Feature**  | `AsParallel()` | `ForAll()` |
|----------|----------------|------------|
| **Role** | Enables parallel query execution | Consumes results in parallel | 
| **Return Type** | `ParallelQuery<T>` | `void` (executes action directly) | 
| **Ordering** | Results may be unordered unless `AsOrdered()` is used | Always unordered unless explicitly ordered | 
| **Best Use Case** | Large datasets, CPU-bound filtering/aggregation | Fast parallel consumption (logging, side-effects) | 

#### Important Considerations- 
- **Ordering:** PLINQ does not guarantee order unless you use `.AsOrdered()`.
- **Overhead:** Parallelism adds thread management overhead. For small datasets, sequential LINQ may be faster.
- **Side Effects:** `ForAll()` executes actions concurrently, so avoid shared state without synchronization.

- **Comparison with** `Parallel.ForEach()`:
- `Parallel.ForEach()` is imperative (loop-based).
- `AsParallel()` is declarative (query-based), often cleaner for complex filtering and projections.

#### When to Use- 
- Use `AsParallel()` when:
    - Dataset is large (thousands+ elements).
    - Operations are CPU-intensive (e.g., math, parsing).

- Use `ForAll()` when:
    - You need to apply side-effects (e.g., logging, writing output) quickly.
    - Order of results doesn’t matter.

### Exception Handling in PLINQ
- Since multiple partitions run in parallel, multiple exceptions can occur at once.
    → PLINQ wraps them inside an `AggregateException`.

#### Example: Exception Handling in PLINQ

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        try
        {
            var numbers = Enumerable.Range(1, 20);

            var results = numbers.AsParallel()
                                 .Select(n =>
                                 {
                                     if (n % 10 == 0)
                                         throw new InvalidOperationException($"Bad number: {n}");
                                     return n * 2;
                                 })
                                 .ToList();
        }
        catch (AggregateException ae)
        {
            foreach (var ex in ae.InnerExceptions)
            {
                Console.WriteLine($"Caught: {ex.GetType().Name} - {ex.Message}");
            }
        }
    }
}
```
#### What happens here:
- Numbers divisible by 10 trigger an exception.
- PLINQ collects all exceptions from different threads.
- They’re wrapped in `AggregateException`.
- You can iterate over `ae.InnerExceptions` to inspect each one.

#### Key Points to Remember
- Always catch `AggregateException`: That’s the standard wrapper for PLINQ errors.
- `InnerExceptions`: Contains one or more exceptions thrown by parallel tasks.
- `Flattening`: You can call `ae.Flatten()` to collapse nested AggregateExceptions.
- **Side-effects caution:** Exceptions inside `ForAll()` or other side-effecting operators also bubble up as `AggregateException`.

#### Best Practices
- Use fine-grained exception handling inside the query if possible:

```csharp
.Select(n =>
{
    try { return DangerousOperation(n); }
    catch { return -1; } // fallback
})
```

- For critical errors, let them bubble up and handle them outside with `AggregateException`.
- If ordering matters, remember that exception handling doesn’t change ordering guarantees (`AsOrdered()` still applies).


### Cancellation in PLINQ
- PLINQ queries can be canceled mid-execution using a `CancellationToken`.
- You pass the token into the query using `WithCancellation(token)`.
- If cancellation is requested, PLINQ throws an `OperationCanceledException` wrapped inside an `AggregateException`.

#### Example: Cancelling a PLINQ Query
```csharp
using System;
using System.Linq;
using System.Threading;

class Program
{
    static void Main()
    {
        var cts = new CancellationTokenSource();
        var token = cts.Token;

        try
        {
            var numbers = Enumerable.Range(1, 1000000);

            var query = numbers.AsParallel()
                               .WithCancellation(token)
                               .Select(n =>
                               {
                                   // Simulate work
                                   if (n % 10000 == 0)
                                       Thread.Sleep(10);

                                   return n * 2;
                               });

            // Start query execution in background
            var task = System.Threading.Tasks.Task.Run(() =>
            {
                foreach (var result in query)
                {
                    Console.WriteLine(result);
                }
            });

            // Cancel after 100 ms
            Thread.Sleep(100);
            cts.Cancel();

            task.Wait();
        }
        catch (AggregateException ae)
        {
            foreach (var ex in ae.InnerExceptions)
            {
                if (ex is OperationCanceledException)
                    Console.WriteLine("Query was canceled.");
                else
                    Console.WriteLine($"Error: {ex.Message}");
            }
        }
    }
}
```
#### Key Points
- `WithCancellation(token)`: Attaches a cancellation token to the query.
- **Exception type**: Cancellation triggers `OperationCanceledException`, wrapped in `AggregateException`.
- **Graceful stop**: All partitions stop processing when cancellation is signaled.
- **Best practice**: Always wrap PLINQ queries in try/catch for AggregateException.

#### Best Practices
- Use cancellation for long-running queries where user interaction may stop execution.
- Always check for cancellation when consuming results with `ForAll()` or `foreach`.
- Combine with timeout logic using `CancellationTokenSource.CancelAfter(milliseconds)`.


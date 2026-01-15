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

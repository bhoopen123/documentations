## Async - Await

- Everything after `await` keyword is considered a continuation. When program runs to the await keyword, the calling thread is immediately released, so it is free to do any other thing.

- Returns a value

- Throws exceptions of the task if there is any.

- Manages the synchronization context.


- You can't have Concurrency without Asynchrony but you can have Asynchrony without Concurrency.
- `lock(this)` is equivalent to creating a `public` object inside the class and using this public object to lock on the internal implementation excution. So anyone having object of class can also access the lock object which should be private. 
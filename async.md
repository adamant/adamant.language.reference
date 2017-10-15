# Async and Await

Principle: Async Everywhere - use async in lots of places

Operations that should be async, Disk, Network, Screen (writing to Console can be slower thatn writing to file), inter thread and process communication, and sleeping.

  * Principle: No Blocking Operations - it should be impossible to block a thread
  * Principle: Don't divide the world into red and green methods

Idea: the equivalent of C# async void methods would be safe for throws void methods (not sure anymore what this was supposed to mean)

It still seems to make sense to indicate that some actions are expected to be async and that should be dealt with.  Kind of reminds me of the distinction of different 

There really needs to be back-pressure and throttling in async operations.

There are different kinds of functions:

  * Expected to be prompt, synchronous
  * Expected to be asynchronous

Independently of that, you have functions that can throw exceptions and those that can't.  Of course, it seems like there could be functions you expect to be prompt, synchronous that aren't.  Also, distinguishing these in the type system seems to lead to fragility problems.  We are implying that we always know in an interface what our subclass implementation will be.  Given you can implement the interface of any class, that feels dangerous.

Coroutine like behavior
  * await a result
  * pull from an iterator
  * push to an observable

iterator and observable are dual, it would be good if they could be unified

Note: Promises will have to carry information about what exceptions they can throw so we can know what exceptions our function can throw

CPU bound vs IO bound.  In C#, use Task.Run for CPU bound, but it sounds like it really depends on who the caller is for whether you want that, so perhaps the rule should be that you push onto the caller to Task.Run you if needed?

Note: Promise<T> may be a good example of a struct that should be a move type. Generally, only one person should be awaiting it.

-------

Idea: Implicit awaits.  Any time the compiler sees a conversion from async T to T it could insert an `await`.  In other languages, that would be horrible, it would confuse the shared state.  But with lifetimes in Adamant, the compiler would correctly enforce mutability.  If it wasn't right you'd get a compiler error and could add an await.

-------

## Best Idea so Far

Functions can be marked `async` and should not return `Promise<T>`.  Any function marked `async` must either be called with `await` or `async`.  Using `await` on an async function causes the current functions execution to block until the async function is complete.  Using `async` causes the async function to run until it blocks, then returns a `Promise<T>` allowing concurrency control.  Functions with an `await` in them must either be marked `async` or `sync`.  The `sync` keyword is used to adapt async code to a synchronous interface and should rarely be used.

Implementation: Async functions are regular functions.  `await` on most async functions causes essentially zero overhead.  When an `await` occurs on an unfulfilled promise or otherwise blocks, then control is returned to the nearest containing `async`.  There an unfulfilled promise is created and execution continues.  It is at this point that the stack branches so that future calls will be made on a new stack branch.  The arm between an `async` and the blocked `await` is called an *activity*.

No blocking is allowed to block a system thread.  All "blocking" should release the OS thread to the threadpool.

There is a threadpool that runs tasks.  The threadpool has two zones a pool of regular task threads and a pool of blocking task threads.  If a thread is stuck on a single task too long it is removed from the regular pool and places in the blocked pool.  This is to ensure there are always a number of threads available for short tasks nearly equal to the number of CPUs.

If you want something to run concurrently you can do `async Async.Task(()->{...})`.  If it is expected to be computationally long or is calling a blocking external method (blocking external APIs should be avoided at all costs).  Use `Async.BlockingTask()` instead to use a thread from the block task thread pool.  The result will of course be a promise you should eventually await.  However, if there are no exceptions thrown by the task, you can call a method (name?) to disconnect from the result because the task can't fail.  Maybe there should be another function that will cause the process to panic if an exception is thrown from the task.  This makes it clear that promises will have types indicating the exceptions they can throw.

## Alternative Syntax

The keyword `async` doesn't clearly indicate its semantics when used on a function call.  The keywords `begin` or `start` might be better.

The `sync` keyword may not be useful, or may be better handled with a syntax to indicate that you are explicitly overriding a sync method with an async method.  The subclass method is then treated as async unless being called through the base class.

There may be an issue with `await` and `async` being prefix.  This means they don't mix well with function call chains.

	let x = await (await (await Foo()).Bar()).Baz();

This could be addressed by switching to some postfix syntax.  For example, `Foo()^.Bar()^.Baz()^`.  Alternatively, a `.` could automatically await its parameter in an async method.

## Async loops

TODO: async `for`


-----

## Another Approach

Have methods that return `Promise<T>` their implementation can then be declared async to have them work like C# async methods.  You can await a task in any method.  You can also call a sync method with the async keyword so that if it blocks on an async method, you will get a task.
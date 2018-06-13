# Error Handling

Adamant's error handling model is built around a nuanced view of the different kinds of errors that can occur and how they should be handled. It handles errors in a variety of ways.

## Prevent Errors

The first and best strategy for error handling is to prevent them or make them compile errors. Any time you write code that could error at runtime by any mechanism, ask yourself whether it could be redesigned to prevent that error at compile time.

The Adamant language and standard library are built around this error handling approach because by greatly reducing the number of errors that can occur at runtime the correctness, performance and readability of the program is improved.

For example, optional types entirely eliminate null pointer errors that bedevil code written in other languages. Instead looping through an array by index, the preferred idiom which the language directs programmers to is iterators. Code using iterators will never cause an array index out of bounds exception. At the same time, the code is usually simpler and better conveys the intent of the code.

When reading about the error handling mechanisms available, remember that a large percentage of Adamant code is error free by design. It doesn't have to concern itself with any of these.

## Recoverable vs. Unrecoverable Errors

The error handling model is focused around a principled distinction between recoverable and non-errors.

* A *recoverable error* is an anticipated state that detected by some programmatic validation. That is, the code has examined the state of the world and determined it is not possible to make progress on the computation in some manner. Recoverable errors generally include:
  * Invalid user input
  * Invalid data input (files, network data etc.)
  * Transient network failure
  * Hardware Failure

  In these cases the program is expected to recover. The developer must plan for and handle these errors. Possible responses include signalling the situation the user, retry, or abandoning the current task. Despite being called an error, it is a *predictable* and *planned* situation.
* An *unrecoverable error* is any kind of error the programmer didn't expect. That is to say that unrecoverable errors are *bugs*. Input may not have been properly validated. Logic may be written wrong. Often these are not detected at the moment they occur, but propagate through the program state until they are detected because of some unexpected consequence. By that point, nothing can be assumed. All program state is suspect.

Most languages do not make the distinction between recoverable and unrecoverable errors or try to handle them in substantially similar ways.

Given that bugs are inherently unrecoverable, Adamant treats them that way by *abandonment*.

For a detailed analysis of error handling options, read about [The Error Model](http://joeduffyblog.com/2016/02/07/the-error-model/) of Midori by Joe Duffy.

## Unrecoverable Errors

All bugs are unrecoverable errors and cause abandonment. Abandonment terminates the process without running any destructors or cleaning up anything. These bugs are caught and abandonment triggered by [contracts and assertions](contracts-assertions.md).

Additionally, [out of memory errors and stack overflow](out-of-memory.md) are an edge case that are semi-recoverable, but can lead to abandonment.

**TODO:** Need to create a way of having abandonment scopes that can be recovered. Rust recovery doesn't seem to provide adequate isolation

## Recoverable Errors

Recoverable errors are further divided into errors which can typically be handled locally where they occur versus ones that typically must be non-locally handled farther up the call stack. The distinction between these two is fuzzy, so the recoverable error handling mechanisms let you mix the two and convert an error from one kind to the other.

### Non-local Error Handling

Non-local errors are usually those involving file or network IO errors. Often the best response is to back out of what is being done. They are dealt with through [exceptions](exceptions.md).

### Local Error Handling

Local errors are usually those involving input or data validation. Often the best response is to record the error, either by logging or reporting it to the user and then substituted a default or error indicating value. These errors are dealt with through the [result type](result.md).

## Effects

Exceptions are actually just one instance of a more general mechanism called effect typing which allows you to manage the side effects and attributes of your code. There are [effects](effects.md) for tracking many different things.

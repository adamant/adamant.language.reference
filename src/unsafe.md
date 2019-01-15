## Unsafe

### Unsafe Context
The `unsafe` keyword is used to create an unsafe context. An unsafe block is introduced with the `unsafe` keyword followed by a block. An unsafe expression is introduced by the `unsafe` keyword followed by an expression in parenthesis.

```adamant
unsafe
{
    // unsafe code
}
x = unsafe(/* unsafe expression */);
```

An unsafe context is necessary to:

1. Access or modify a pointer
2. Access or update a mutable static variable
3. Call unsafe functions
4. Call untrusted functions (see [Trusted Packages](#TrustedPackages))

It is a compile time error to perform any of those operations outside of an unsafe context.

## Unsafe and Safe Functions

Any function containing an unsafe context must be marked as either `unsafe` or `safe`. This makes explicit the safety contract provided by the API. A function marked `safe` is promising that even though it contains unsafe code, using the function is safe.

Functions that do not contain unsafe contexts may also be marked `unsafe` to indicate that their use can lead to undefined behavior in some other way. It is recommended this be used sparingly. However, it might be appropriate if the method code violates invariants of the class. For example, `string` guarantees that it contains a valid utf8 encoding. If there were a slice method that returned a string slice that may not be valid utf8 because it didn't check if the index split a code point, that method should be marked `unsafe`.

It is a compile time error to mark a function `safe` that contains no unsafe contexts.

## Unsafe and Safe Classes and Structs?

**TODO:** should it be necessary to mark the class as a whole as safe or unsafe? Exactly where would the use of an unsafe type cause an error? Does an unsafe class mean all of its constructors are unsafe?

## Trusted Packages

Packages referenced from adamantforge.com may be trusted or untrusted. By default all packages in the `system` namespace are trusted while all other packages are untrusted. This can be changed for individual packages using the `trusted` attribute of the project file. Any function marked `unsafe` or `safe` in an untrusted package is untrusted. Note that this can propagate from one package to another. Any function that calls an untrusted function is untrusted. Calling an untrusted function requires an unsafe context.

## Docs

In API docs, `unsafe` functions are marked as such. `safe` functions are not distinguished from `unsafe` functions. Rather the docs indicate which functions require trust and what packages must be trusted to call them.

## Rationale

Both Rust and C# treat unsafe as always creating an unsafe context even when applied to functions or traits. This means that an unsafe function may carry out an unsafe operation at any point. While it is certainly true that "safe" code in the method may introduce a bug in the "unsafe" code, there is value in highlighting the segments of code that are unsafe.

Rust and C# have no equivalent of the `safe` keyword. Requiring developers to mark the safety of the function adds an extra safety check, ensuring that a developer explicitly acknowledges whether they are promising safety to their callers.

## Unsafe References

An old implementation of optional types indicated that it might be useful to have references that may not be initialized. The idea is that a reference could be marked `unsafe`. Then you could set it to `uninitialized` to indicate you didn't want to initialize it or that the current value should be thrown away. Accessing an unsafe reference is unsafe because the compiler can't guarantee it has been initialized.

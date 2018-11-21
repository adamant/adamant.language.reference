# Basic Types

### Issues

Does a C# checked context throw for loss of fractional digits? I don't think it does. That seems like it could be an issue in some situations.

## `any`

A variable of the type `any` can have any reference or value type placed in it.

**TODO:** Can we say `any` supports function types too?

Additionally, `@any` can be assigned from any pointer type.

**TODO:** Can `@x` where `x` is of a reference type return a value of type `@any` so we can compare reference equality?

## Types

`type` and `metatype` can these be pseudo references so the programmer doesn't need to worry about them? What about constructing generic types? If they are reference types, should the be capitalized?

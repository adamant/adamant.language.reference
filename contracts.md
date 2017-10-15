# Contracts

Contracts are a special type of attribute.  Like all attributes they are introduced by the at sign, but are followed by an expression instead of arguments.  There are three kinds of contracts:

  * `@requires` - enforces preconditions.  Has immutable access to all parameter values and the public interface of the current class.
  * `@ensures` - enforces postconditions.  Has access to the original value of arguments, new and old state of the current class and return value.  Can access the return value with the name `return`
  * `@invariant` - must old before and after every method of a class.  I assume this is only public methods, but perhaps there needs to be a way to declare invariants that apply to private methods as well.

Not sure how contracts being attributes interacts with the fact that they need to apply to subtyping.

## Enforcement
  * By default, all contracts are checked at runtime.
  * The compiler is free to prove contracts false, and issue compile-time errors.
  * The compiler is free to prove contracts true, and remove these runtime checks.

## Accessing

Midori used `old` keyword as a way to reference the previous state of `this` and then `old()` as a function of an argument to access the previous value of an argument.  One concern about that is whether it might require copying the value.
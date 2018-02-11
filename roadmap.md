# Implementation Roadmap
Versions of the Adamant compiler before v1.0 will only implement subsets of the language.  This enables the boostrapping of the compiler. Rather than creating separate specifications for these subsets of the language, they will be described by listing which features of the language are supported and any restrictions on those features.

The v0.5.0 version will represent a major milestone in the implementation of the language. Below is a roadmap of when features are likely to be implemented.

## Features Probably in v0.5.0
  * Backslash [Escaped Identifiers](escaped-identifiers.md) - easy but get lots of approval
  * Ownership and Borrow Checking
  * Async/await?
  *

## Features that may be in v0.5.0
  * [Operator Overloading](operator-overloading.md)
  * Properties
  * Overload Functions on Number of Arguments
  * Contract Parsing - `requires` and `ensures`
  * Re-declaring variable bindings with `let`
  * Types: `int128`, `uint128`
  * `params` keyword
  * `unsafe`

## Features Probably *Not* in v0.5.0
  * Types: `float128`, `fixed*.*`, `decimal*`
  * Overload Functions on Argument Types
  * Type Inference
  * Ownership Inference
  * Contract Execution - `requires` and `ensures`
  * Constant Folding and Static Execution - that is at compile time
  * [Generators](generators.md) i.e. `yield`
  * [Bitwise operations](bitwise-operations.md)
  * [`unchecked` context](unchecked.md)
  * [Tuples](tuples.md)

## Features Probably in v1.0.0
  * Compiler & Toolchain Versioning - need to be able to manage changes in the language
  * No Standard - need to make sure the standard lib isn't too heavily based on the OS
  * Hygienic Macros Plan - need to know where macros are likely to go
  * Reflection Plan - need to make sure there is a path to reflection

## Features Probably *after* v1.0.0
  * [Partial Classes](partial-classes.md)
  * Hygienic Macros - but will not having them change the standard library?
  * [Package Qualifiers](package-qualifier.md)

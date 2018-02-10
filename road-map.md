# Implementation Road Map
Versions of the Adamant compiler before v1.0 will only implement subsets of the language.  This enables the boostrapping of the compiler.  Rather than creating separate specifications for these subsets of the language, they will be described by listing which features of the language are supported and any restrictions on those features.

The v0.5.0 version will represent a major milestone in the implementation of the language. Below is a list of features that could be omitted organized by whether they are likely to be omitted in v0.5.0.

## Features Probably in v0.5.0
  * Backslash [Escaped Identifiers](escaped-identifiers.md)

## Features that May be in v0.5.0
  * [Operator Overloading](operator-overloading.md)
  * Properties
  * Overload Functions on Number of Arguments
  * Contract Parsing - `requires` and `ensures`
  * Re-declaring variable bindings with `let`
  * Types: `int128`, `uint128`
  * `params` keyword
  * `unsafe`

## Features Probably *Not* be in v0.5.0
  * Types: `float128`, `fixed*.*`, `decimal*`
  * Overload Functions on Argument Types
  * Type Inference
  * Ownership Inference
  * Contract Execution - `requires` and `ensures`
  * Constant Folding and Static Execution - that is at compile time
  * `yield`
  * Bitwise operations
  * `unchecked` context

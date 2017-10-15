# Implementation Road Map
Versions of the Adamant language before v1.0 are termed Pre-Adamant.  These languages are generally restrictions of the Adamant language.  This allows earlier compilers and interpreters to be much simpler.

The primary dividing line in Pre-Adamant versions will be the v0.5.0 version.  This is the planned version for the cutover from the Exploratory Interpreter written to C# to the actual compiler written in Pre-Adamant.

Generally, Pre-Adamant can be described by the Adamant v1.0 features that are omitted.  Below is a list of features that could be omitted organized by whether they are likely to be omitted in v0.5.0 since that is the important cutover point.

## Features Probably in v0.5.0
  * Escaped identifiers - identifiers escaped with a backtick

## Features that May be in v0.5.0
  * Operator Overloading
  * Properties
  * Overload Functions on Number of Arguments
  * Contract Parsing - requires and ensures
  * Re-declaring variable bindings with let
  * Types: int128, uint128
  * `params` keyword
  * `unsafe`?

## Features Probably *Not* in v0.5.0
  * Types: float128, fixed*.*, decimal*
  * Overload Functions on Argument Types
  * Type Inference
  * Ownership Inference
  * Contract Execution - requires and ensures
  * Constant Folding and Static Execution - that is at compile time
  * Yield?
  * Bitwise operations
  * `unchecked` context

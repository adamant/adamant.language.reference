# Value Types

We have covered value types which are the majority of types you will deal with in Adamant.  However, there are also value types which are important to understand as many of the predefined types in Adamant are value types.  Again a value type is a type that is passed by reference.  So they always reside on the stack or directly in the object holding them.  When they are passed between functions they are moved or copied.  For both of those reasons, it is a good practice to limit the amount of memory used by a value type.  In Adamant, any type declared as a `struct` is a value type.  In general, you should reach for a `class` first and only use a `struct` when you particular scenario fits the few situations they are good for.  It's important to note that just because you pass a struct by value doesn't mean a copy of the object is happening.  In many important cases, the compiler is able to optimize those copies out of existence.

## Move Semantics

## Copy Semantics

## Explicit Copy

## Pseudo References
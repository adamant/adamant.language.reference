# Pseudo References

You've learned all about the ownership system in Adamant. About reference types, value types, variable references and lifetimes, but some types you use on a regular basis may not seem like they fit in that list. It is possible using the features of Adamant to create types that behave differently then one might expect. An important case of this are *pseudo references*. These are value types that in some way appear as if they are actually reference types. Examples include `string`, `Array<T>`, and `CountedRef<T>`. Let's explore how pseudo references work.

## `string`

The predefined string type is defined as a `struct` for efficiency. At the same time, passing around a large string doesn't constantly copy all that data. Furthermore, if you've used methods like `Substring()` you know that strings can have lifetimes as if they were references.

## `CountedRef<T>`

The `CountedRef<T>` defined in the standard library is a true pseudo reference. It is designed to behave as much life a reference to an object of type `T` as possible.
# Casting with `as`

`as!` will cast a value to a given type and panic if the cast fails.  `as?` will cast a value to the nullable of the given type, returning error `Result<T>` if the cast fails.  `as` will perform a statically safe cast.  That is a cast up or widening conversion

NOTE: downcasting is never allowed.
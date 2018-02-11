# Struct Constructors

Struct constructors are very similar to class constructors, except they need to take their `self` parameter as either `ref mut self` so they can safely mutate it. Notice, this is an example where the distinction between `var` and `mut` makes sense on struct types because you can mutate self, but not assign it a new value.
# Class Value Type

An class value type is the value/struct type for a specific class. Essentially, the class value type is the struct type for the data structure on the heap that is referenced. Given a class `T` the class value type is `^T`. Think of this as unreferencing the reference type to give the underlying value type. Class value types are a way to force a reference type to be allocated on the stack or locally as if it were a struct type. They also enable the use of pointers to types declared with classes.

Note that given two reference types `S <: B` there is no subtype relationship between `^S` and `^B`. This is because they might be of different size and so assigning one to the other might lead to object slicing as can happen in C++.

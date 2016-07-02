# Generics

For value type `V` and reference type `R`, the type parameter `T` can be assigned any of:

  * `V`
  * `ref V`
  * `ref var V`
  * `R`
  * `mut R`
  * `ref var R`
  * `ref var mut R`

// TODO what about own?

Types can then be constructed from `T` in any of the following ways:

  * `T`
  * `ref T`
  * `ref var T`

Note that you can't specify the mutability of `T`.  That is inherent to the type parameter.

To the code using type parameter `T`, it behaves have a value with move semantics and no copy function.  This only changes if there are generic constraints.


## Constraints

	where T: class // Then `ref T` becomes invalid
	where T: struct
	where T: move struct
	where T: copy struct
	where T: copy() // T has a copy method, explicit or implicit.  must be used explicitly though
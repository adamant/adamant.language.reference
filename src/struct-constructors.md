## Struct Constructors

Structs are initialized using [struct initializers](struct-initializers.md) instead of constructors. However, reference structs masquerade as references and may allocate space on the heap as part of their initialization. To allow reference structs to fully operate as references, they are allowed to have constructors.

Struct constructors are declared with the `new` keyword and called like constructors using the `new` keyword. However, semantically, they behave exactly like initializers. By convention, these should only be used for initializers that allocate new data structures on the heap. The lifetime of the struct returned from a constructor is "`owned`".

A struct with a constructor will not have a default initalizer created for it.

# Struct Constructors

Structs do not have true constructors because they are not allocated on the heap like classes. However, pseudo-reference structs can have pseudo-constructors to enable reference like usage. These constructors are declared with the "`new`" keyword exactly as class constructors. However, they function the same as struct initializers. By convention, these should only be used for initializers that allocate new data structures on the heap. The lifetime of the struct returned from a pseudo-constructor is "`owned`".

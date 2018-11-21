# Types

Types in Adamant are divided into two main categories: *value types* and *reference types*. Both value and reference types may be *generic types*, which take *generic parameters*. Generic parameters can be types (both value and reference types) or values. Generic parameters that are types are called *type parameters*. Finally, *optional types* can be value types or reference types.

```grammar
type
    : value_type
    | reference_type
    | type_parameter
    | optional_type
    ;
```

The values types can be further divided into a number of subcategories. Most value types are *struct types*. The *simple types* are predefined value types identified with keywords. There are also *optional types*.

```grammar
value_type
    : struct_type
    | simple_type
    | class_value_type
    ;
```

## Class Value Type

A class value type is the value type for a specific class. Essentially, the class value type is the struct type for the data structure on the heap that is referenced. Given a class "`T`" the class value type is "`^T`". Think of this as unreferencing the reference type to give the underlying value type. Class value types are a way to force a reference type to be allocated on the stack or locally as if it were a struct type. They also enable the use of pointers to classes.

Note that given two reference types "`S <: B`" there is no subtype relationship between "`^S`" and "`^B`". This is because they might be of different size and so assigning one to the other might lead to object slicing as can happen in C++.

```grammar
class_value_type
    : "^" class_reference_type
    ;
```

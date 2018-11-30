# Types

Types in Adamant are divided into two main categories: *value types* and *reference types*. Both value and reference types may be *generic types*, which take *generic parameters*. Generic parameters can be types (both value and reference types) or values. Generic parameters that are types are called *type parameters* and can be used as types. *Optional types* modify both value types or reference types. Finally, *variable reference types* aka *ref types* are a special form of reference with their own rules.

```grammar
type
    : value_type
    | reference_type
    | type_parameter
    | optional_type
    | variable_reference_type
    ;
```

The values types can be further divided into a number of subcategories. Most value types are *struct types*. The *simple types* are predefined value types identified with keywords. The *tuple type* is a predefined type for multiple vales. Finally, *class value types* are the value types referenced by class types.

```grammar
value_type
    : struct_type
    | simple_type
    | tuple_type
    | class_value_type
    | pointer_type
    ;
```

Note that while value types are declared to be subtypes of reference types, the reality is more complicated. Given value type `S` that is a subtype of reference type `T`, then `ref S <: T`. There is an implicit boxing conversion from `S` to `T$owned`.

# Types

Types in Adamant are divided into two main categories: *value types* and *reference types*. Both value and reference types may be *generic types*, which take *generic parameters*. Generic parameters can be types (both value and reference types) or values. Generic parameters that are types are called *type parameters*. Finally, *optional types* can be value types or reference types.

```grammar
type
    : value_type
    | reference_type
    | type_parameter
    | optiona_type
    ;
```

The values types can be further divided into a number of subcategories. Most value types are *struct types*. The *simple types* are predefined value types identified with keywords. There are also *optional types*.

```grammar
value_type
    : struct_type
    | simple_type
    | optional_type
    ;
```

## Composite Types

Composite types allow other data types to be composed into further types. All type operators have the same precedence. Order of type operations can be controlled using parenthesized expressions.

```grammar
composite_type
    : union_type
    | intersection_type
    | "(" type ")"
    ;
```

### Union Types

Union types are the union of other data types. That is, a value of any of the types being unioned is a legal value for the union type. There are limitations on which types may be unioned. For a union type to be valid, one of the following must be true:

1. All types being unioned are reference types or optional reference types.
2. All types being unioned are members of a single enum struct.

Methods and properties common to all types in the union type can be directly accessed on a value of the union type. Other methods and properties require that the value be destructured to the specific type using `match` etc.

```grammar
union_type
    : type "|" type
    ;
```

### Intersection Types

Intersection types are the intersection of other data types. For a value to be of the intersection type, it must be a subtype of all the types being intersected. Only reference types and optional reference types can be intersected.

```grammar
intersection_type
    : reference_type "&" reference_type
    ;
```

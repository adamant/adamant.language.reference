## Tuple Types

The tuple types are a family of generic types. Like the simple types, they are identified by the keyword "`tuple`". If all the type parameters are implicitly copyable, then the tuple is implicitly copyable. If all the type parameters are implicitly *or explicitly copyable* then the tuple is explicitly copyable. If any type parameter is not copyable, the tuple is a move type.

```grammar
tuple_type
    : "tuple" "[" type{",", 0, } "]"
    ;
```

### Tuple Operations

| Operation            | Syntax        |
| -------------------- | ------------- |
| Get number of values | `t.count`     |
| Get value *n*        | `t.at[`*n*`]` |

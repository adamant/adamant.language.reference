## Reference Types

```grammar
reference_type
    : object_reference_type
    | function_type
    | "Any"
    | variable_reference_type
    | pointer_type
    ;
```

### `Any` Type

All object reference and function types are a subtype of the "`Any`" type.

### Pointer Types

```grammar
pointer_type
    : "@" value_type
    | "@" "Any"
    ;
```

#### `@Any` Type

The pointer to any type "`@Any`" is a pointer type that places no limits on what may be at the memory pointed to. It is the equivalent of a void pointer in C/C++ or Rust.

**TODO:** Can `@x` where `x` is a variable of reference type return a value of type `@Any` so we can compare reference equality?

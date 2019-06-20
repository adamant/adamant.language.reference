## Pointer Types

Pointer types are used by unsafe code to directly address memory. Only pointers to value types are supported. This ensures that pointers can be implemented as simple addresses compatible with pointers in languages like C. Reference types may require fat pointers to enable virtual method dispatch. Thus pointers to reference types are not valid. However, a pointer to a class value type is valid.

Pointer types are non-nullable. To represent a nullable pointer use an optional pointer type (i.e. `@int?`).

```grammar
pointer_type
    : "@" value_type
    | "@" "Any"
    ;
```

### `@Any` Type

The type "`@Any`", is a pointer type that places no limits on what may be at the memory pointed to. It is the equivalent of a void pointer in C/C++ or Rust.

References can be compared for reference equality by converting them to "`@Any`".

```adamant
let f = new Foo();
let g = f;

if f as @Any == g as @Any // reference equality
{
    // ...
}
```

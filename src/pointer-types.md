## Pointer Types

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

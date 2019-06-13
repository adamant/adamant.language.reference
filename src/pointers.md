## Pointers

Sometimes, low level control of memory and pointers is needed. For this purpose, pointers can be used. Pointers are very similar to pointers in languages like C, C++ and Rust. Many operations on pointers are inherently dangerous and require an unsafe context.

Only pointers to value types can be declared. Because reference types allow for a subtype relationship, references are "fat." Meaning they contain both a pointer to the data and a pointer to a vtable. Pointers don't support this, they are "thin." Thus it must be possible to know the exact type and size at compile time. This is true of value types.

### Address Of

A pointer to a value can be obtained using the address of operator `@`. For value types and references to variables, the address of operator takes the address of the variable so the pointer points to the variable which is typically on the stack. Taking the address of something is a safe operation.

```adamant
let x = 6;
let ptr_x = @x; // points to `x` on the stack
let y = ref x;
let ptr_y = @y; // points to `y` on the stack
```

References can be safely cast to a void pointer (see below). This is used to good effect to compare two references for reference equality.

```adamant
let f = new Foo();
let g = f;

if f as @void == g as @void // reference equality
{
    // ...
}
```

The address operator is read as either "address of x" or "pointer to x"

### Pointer Types

Pointer types are simply prefixed with the `@` sign. The type is then read as either "address of T" or "pointer to T". Just as with references, pointers refer to immutable values by default and must use `mut` to allow mutation of the value. Pointer types can be freely combined with references to produce complex types.

```adamant
public struct Foo {}
public struct Bar {}

let f: Foo = new Foo();
let ptr_f: @Foo = @f;
let b: mut Bar = new Bar();
let ptr_b: @mut Bar = @mut b;
let x: int = 6;
let ptr_x: @int = @x;
var y: int = 7;
let ptr_y: @mut int = @mut y; // similar to taking `ref var`
let r: ref var int = ref var y;
let ptr_r: @ ref var int = @r;
```

### Converting References to Pointers

Given an existing reference to a value type, it can also be cast to a pointer.

```adamant
let x: int = 5;
let ref_x: ref int = ref x;
let ptr_x: @int = ref_x as @int;
```

### Dereferencing Pointers

Accessing the value a pointer refers to is called dereferencing. This is done using the dereferencing operator `^` which is a prefix operator. To dereference and call a method, the special dereference access operator `^.` can be used. Dereferencing is an unsafe operation.

```adamant
unsafe
{
    let x: int = 5;
    let ptr_x: @int = @x;
    let y: int = ^ptr_x;
    let f: Foo = new Foo();
    let ptr_f: @Foo = @f;
    ptr_f^.method();
}
```

The dereference operator can also be used to dereference variable references if needed. This is a safe operation since variable references are always valid.

```adamant
var x: int = 6;
let y: ref var int = ref var x;
^y = 7; // assigns 7 into x
y = 8; // because of types involved, dereference is implied. The same thing doesn't happen with pointers
```

### Nullable Pointers

By default, all pointer types are non-nullable. That is, they may refer to invalid memory locations, but they can never be null. To create a nullable pointer type, use optional types. So a nullable pointer to `T` would be `@T?`. Note that the `@` has higher precedence than `?` so this is parsed as `(@T)?` rather than `@(T?)`.

### Void Pointers

There are special pointer types for pointers without reference to the type of data they hold. These are void pointers, `@void` and `@mut void`. They can't be dereferenced, but must be cast to some other pointer type to be used.

### Ownership and Pointers

A pointer doesn't "own" or even borrow the value it points to. The pointed to value will be disposed of whenever it would normally be disposed. Note, that the pointer type doesn't track ownership and it is up to the developer to ensure that the memory is deleted.

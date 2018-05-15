# Raw Pointers

Classes declare reference types whose values are always stored and passed by reference. Sometimes, more low level control is needed of references. For this purpose, raw pointers can be used. Raw pointers are very similar to pointers in languages like C, C++ and Rust. Many operations on pointers are inherently dangerous and require an unsafe context.

## Address Of

A pointer to a value can be obtained using the address of operator `@`. For reference types, the address of operator returns a pointer to the object being referenced. That is, it points to the object which is typically on the heap rather than pointing to the reference which then points to the object. For value types and references to variables, the address of operator takes the address of the variable so the pointer points to the variable which is typically on the stack.

Taking the address of something is a safe operation. This is used to good effect to compare two references for reference equality.

```adamant
let f = new Foo();
let g = f;

if @f == @g // reference equality
{
    let x = 6;
    let ptr_x = @x; // points to `x` on the stack
    let y = ref x;
    let ptr_y = @y; // points to `y` on the stack
}
```

The address operator is read as either "address of x" or "pointer to x"

## Pointer Types

Pointer types are simply prefixed with the `@` sign. The type is then read as either "address of T" or "pointer to T". Just as with references, pointer refer to immutable object by default and must use `mut` to allow mutation of the value. Pointer types can be freely combined with references to produce complex types.

```adamant
let f: Foo = new Foo();
let ptr_f: @Foo = @f;
let b: mut Bar = new Bar();
let ptr_b: @mut Foo = @mut b;
let x: int = 6;
let ptr_x: @int = @x;
var y: int = 7;
let ptr_y: @var int = @var y; // similar to taking `ref var`
let r: ref var int = ref var y;
let ptr_r: @ ref var int = @r;
```

## Converting References to Pointers

Given an existing reference, it can also be cast to a pointer.

```adamant
let f: Foo = new Foo();
let ptr: @Foo = f as @Foo; // TODO not certain allowing this is a good idea
let x: int = 5;
let ref_x: ref int = ref x;
let ptr_x: @int = ref_x as @int;
```

## Dereferencing Pointers

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

The dereference operator can also be used to dereference variable references if needed. This is a safe operation since varible references are always valid.

```adamant
var x: int = 6;
let y: ref var int = ref var x;
^y = 7; // assigns 7 into x
y = 8; // because of types involved, dereference is implied. The same thing doesn't happen with pointers
```

## Nullable Pointers

By default, all pointer types are non-nullable. That is, they may refer to invalid memory locations, but they can never be null. To create a nullable pointer type, use optional types. So a nullable pointer to `T` would be `@T?`. Note that the `@` has higher precedence than `?` so this is parsed as `(@T)?` rather than `@(T?)`. Nullable pointers do not force you to check them for null before dereferencing them?

## Void Pointers

There are special pointer types for pointers without reference to the type of data they hold. These are void pointers, `@void` and `@mut void`. They can't be dereferenced, but must be cast to some other pointer type to be used.

## Ownership and Pointers

A pointer doesn't "own" or even borrow the value it points to. The pointed to value will be disposed of whenever it would normally be disposed. In order for a reference to not be destructed at the end of the scope, ownership must be moved into the pointer. Note, that the pointer type doesn't track ownership and it is up to the developer to ensure that the memory is deleted. Moving ownership can be done using the `move` operator just as it is with function calls. There is special case handling for taking the pointer of a new expression. In that case, `move` is implied.

A pointer that owns its memory needs to be deleted using the delete operator. Deleting is an unsafe operation.

```adamant
f() -> Foo
{
    return new Foo();
}

main() -> void
{
    let a = @move f(); // pointer `a` "owns" the referenced memory and it will have to be manually deleted
    let b = @new Foo(); // move is implied here so that `b` "owns" the memory
    // Do stuff
    unsafe
    {
        delete a; // Deletes the memory allocated for the object pointed to by `a` and calls destructor as needed
        delete b; // Deletes the memory allocated for the object pointed to by `b` and calls destructor as needed
    }
}
```

**TODO:** There needs to be a way to force a value to stay alive to a certain point so that the pointer remains valid. Or maybe unsafe blocks affect lifetimes to ensure values are destructed at the end of the block or after the block as appropriate.

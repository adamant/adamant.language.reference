# Memory Allocation

The standard library provides functions for allocating memory directly. These don't normally need to be used. Consistent with the limitations that pointers can only point to value types, these functions restrict the types they are allocating for to value types.

## Allocating Raw Memory

The `allocate()` and `free()` functions are in the standard library. The memory contents are not initialized.

```adamant
let x: @byte = allocate(45); // allocates 45 bytes
unsafe free(x);
```

## Allocating Memory For Arrays

The memory contents are not initialized.

```adamant
let x: @Struct_Type = allocate[Struct_Type](10); // allocates enough memory for 10 Struct_Type structs, does not initialize them
unsafe free(x); // free memory, does not destruct the points
```

## Allocating Structs on the Heap

Structs can be allocated on the heap using the `system.memory.Boxed[T]` type. `Boxed[T]` is a move type and will automatically free the memory when it goes out of scope.

```adamant
let x = new Boxed[Struct_Type]();
```

## Constructing and Destructing Values in Place

When allocating memory, you are responsible for constructing and destructing the values. This is done with placement init.

```adamant
let x: @mut Point = allocate[Point](2);
unsafe
{
    // Call Constructors with Placement New
    init(x) Point(1, 3);
    init(x+1) Point(9, 4);
    // Call Destructors
    x^.delete(); // calling delete is only valid on pointers
    (x+1)^.delete();
    // Deallocate memory
    free(x); // frees memory, does not destruct points
}
```

## Resizing Memory

```adamant
var x: @mut Point = allocate[Point](2);
x = reallocate[Point](x, 4); // copies contents as raw bytes, may return new pointer value
x = reallocate[Point](x, 8, 2); // resize to 8, only copy 2 items
x = reallocate[Point](x, 2); // just drops memory contents after first two
free(x);
```

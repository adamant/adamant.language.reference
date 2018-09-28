# Raw Memory Allocation

When using `new` to allocate objects for pointers, the memory can be freed using the delete operator. When allocating memory for other purposes, the `allocate()` and `free()` functions can be used.

## Allocating Structs on the Heap

A struct can be allocated on the heap and then referenced using a pointer.

```adamant
let x = @new Struct_Type();
unsafe delete x;
```

Note that `new` is not normally used with structs, and the address of operator is required for this.

## Allocating Raw Memory

The `allocate()` and `free()` functions are in the standard library. The memory contents are not initialized.

```adamant
let x: @void = allocate(45); // allocates 45 bytes
unsafe free(x);
```

## Allocating Memory For Arrays

The memory contents are not initialized.

```adamant
let x: @Point = allocate<Point>(10); // allocates enough memory for 10 Point objects, does not construct them
unsafe free(x); // free memory, does not destruct the points
```

### Placement New and Delete

When allocating memory for arrays, you are responsible for constructing and destructing the values.

```adamant
let x: @mut Point = allocate<Point>(2);
unsafe
{
    // Call Constructors with Placement New
    new(x[0]) Point(1, 3);
    new(x[1]) Point(9, 4);
    // Call Destructors
    x[0].delete(); // calling delete is only valid on pointers
    x[1].delete();
    // Deallocate memory
    free(x); // frees memory, does not destruct points
}
```

## Resizing Memory

```adamant
var x: @mut Point = allocate<Point>(2);
x = reallocate<Point>(x, 4); // copies contents as raw bytes, may return new pointer value
x = reallocate<Point>(x, 8, 2); // resize to 8, only copy 2 items
x = reallocate<Point>(x, 2); // just drops memory contents after first two
free(x);
```

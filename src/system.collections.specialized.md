## `system.collections.specialized` Namespace

# `system.collections.specialized.Raw_Array`

The `Raw_Array[T]` type is a fixed size array that performs no checking on array bounds and does not track the array size. This type is rarely used, but is the foundation for many of the other collection types. As with other objects, the Adamant compiler is able to optimize `Raw_Array` so that the memory allocation occurs on the stack rather than the heap when it can determine the array is small enough for this to be beneficial. The effective declaration of `Raw_Array[T]` is:

```adamant
public mut ref struct Raw_Array[T]
{
    public new(count: size);
    public unsafe init from_pointer[$ref](elements: @T);
    public unsafe at(ref mut self, index: size) -> ref var T;
    public unsafe at(ref self, index: size) -> ref T;
    public slice(ref mut self, index: size) -> mut Raw_Array[T] $< ref;
    public slice(ref self, index: size) -> Raw_Array[T] $< ref;
    public unsafe delete();
}
```

**TODO:** given that the compiler needs special handling to optimize this, should a special construct be added to the language to either replace raw array or allow it to be implemented without special compiler support.

# `system.collections.specialized.Unsafe_Array`

The `Unsafe_Array[T]` type is a fixed sized array with a length that tracks the array size and performs array bounds checked. However, the array starts uninitialized and so is unsafe. This type is rarely used, but is the foundation for many of the other collection types. An unsafe is essentially a wrapper around a `Raw_Array[T]`. The effective declaration of `Unsafe_Array[T]` is:

```adamant
public mut ref struct Unsafe_Array[T]
{
    public let count: size;
    public new(count: size);
    public unsafe at(ref mut self, index: size) -> ref var T;
        requires index < count
    public unsafe at(ref self, index: size) -> ref T;
        requires index < count
    public slice(ref mut self, index: size) $ref -> mut Raw_Array[T];
        requires start < count
        requires start + length <= count
    public slice(ref self, index: size) $ref -> Raw_Array[T];
        requires start < count
        requires start + length <= count
    public unsafe delete();
}
```

## `system.collections.specialized` Namespace

# `system.collections.specialized.Raw_Array`

The `Raw_Array[T]` type is a fixed size array that performs no checking on array bounds and does not track the array size. This type is rarely used, but is the foundation for many of the other collection types. As with other objects, the Adamant compiler is able to optimize `Raw_Array` so that the memory allocation occurs on the stack rather than the heap, when it can determine the array is small enough for this to be beneficial. The effective declaration of `Raw_Array[T]` is:

```adamant
public mut ref struct Raw_Array[T]
{
    public new(count: size);
    public unsafe at(ref mut self, index: size) -> ref var T;
    public unsafe at(ref self, index: size) -> ref T;
    public slice(ref mut self, index: size) -> mut Raw_Array[T] $< ref;
    public slice(ref self, index: size) -> Raw_Array[T] $< ref;
    public unsafe delete();
}
```

**TODO:** given that the compiler needs special handling to optimize this, should a special construct be added to the language to either replace raw array or allow it to be implemented without special compiler support.

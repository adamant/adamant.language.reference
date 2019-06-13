# Appendix: Implementers Notes

The Adamant specification does not dictate a specific implementation for many language features. For example, how virtual method dispatch should be handled. However, there is an implementation in mind when writing the spec. This appendix discusses parts of that implementation and ideas for optimizations to assist compiler developers.

## Whole Program Optimization

It is intended that whole program optimization would be applied, including to referenced packages. This would remove unused code. However, it would also allow the compiler to optimize object references. For example, if a class is never subclassed, then all references to the class must truly be that class and static calls can be made in place of virtual calls. Even if a class is subclassed, dataflow analysis may determine that the subtypes are never passed to a particular function, so that function can treat the class as if there were no subclasses.

## Memory Layout

Adamant is meant to be high performance with fixed, low overhead for common operations. However, in many languages the support for interfaces or multiple inheritance can lead to lots of overhead and slow interface method dispatch. Additionally, Adamant is unique in its support for implementing the interface of a class. This means that a given method may be called with an actual instance of a class or with an unrelated class that is simply implementing its interface. Effectively, this potentially transforms every method call into an interface method dispatch.

An additional wrinkle is added by allowing method implementation on interfaces. In that case it is possible to end up in a diamond inheritance with two different implementations for a given method. The language would then error on an ambiguous call to that method. However, we must support calling the method after casting to one of the two interfaces.

## Vtables

Because any class can implement the traits of any other class, there is not a simple class hierarchy that lends itself to having one or even a few vtable pointers in objects. C++ style vtables would cause all inheritance to effectively be what C++ calls virtual inheritance which is far less efficient.

Alternatively, an approach similar to that described in http://feepingcreature.github.io/oop.html would allow for a simpler system with less overhead for interface method calls. However, since every method call is effectively an interface method call, all vtables (even for the base class) would need to have an object offset. Of course, the compiler could optimize it away in cases where it could prove it would never be needed. This could be a viable approach.

Another vtable approach that could work is a method coloring approach. Essentially, assign any given method a specific slot across all class vtables. This can be done compactly using a graph coloring type algorithm. That is, methods can share a slot if there is no class that implements them both. However, it then becomes unclear how to implement the situation of interfaces with method implementations in a diamond inheritance situation. The two methods override the same method, so they must be in the same slot, but there is only one vtable for the concrete class. So what goes in the vtable in that case? Perhaps you could somehow outlaw this situation, for example by requiring the child class/interface to override the method and select the correct implementation for all instances of that type. **That would be a good limit to add to the language regardless of which implementation approach is taken to prevent getting boxed in later.**

The fact that structs can implement traits also poses a problem. The struct would not normally have a vtable pointer in it, but a reference to the struct on the stack could be converted to a reference to a trait it implements. Where would the vtable pointer come from then?

## Fat Pointers

Instead of using a complicated vtable schemes, the preferred implementation is probably fat pointers. All objects in memory are stored without a vtable or type descriptor pointer. Instead references to objects are "fat". Meaning references are actually a pair of a pointer to the data and a pointer to a vtable like structure with the type descriptor. We will call this structure the *trait table*. Casting therefore involves changing the trait table portion of the reference.

Whenever the compiler is able to determine the concrete type of a value, it can use a regular pointer rather than a fat pointer. Then if it calls a function needing a fat pointer, it can use a constant value for the trait table pointer. This applies to all structs since they can't be inherited from, nor can their interfaces be implemented.

One concern about fat pointers is that they increase total memory usage when there are many references to an object. Rather than a single vtable pointer stored in the object, a vtable pointer is stored with each reference to the object. On the other hand, many objects can be stored without any vtable pointer at all. Furthermore, the planned whole program optimizations and monomorphization optimizations play well with this.

Note: Rust uses fat pointers for trait objects. Swift uses 40 byte fat pointers for all protocol references. Go uses fat pointers for all interface references.

## Pointer Types

Pointer types have been restricted to point to value types because nothing can subtype them and so their size and methods are known at compile time. Thus all pointer types do not need to be fat pointers. They are instead regular pointers the size of addresses on the given platform.

## Trait Tables

Trait tables will need to contain both function pointers to the various methods, but also information about the type and its relation to any base class trait tables. One possible structure for this would be that at address zero of the trait table would be a pointer to the type object of the type. This would be used for reflection. At address one could be the address of the destructor function since all objects will have a destructor even if it is supplied by the compiler. If a type truly has no destructor, this pointer could point to a no-op function or simply be null. This would then be followed by slots for all the virtual methods of the type and its base classes starting from the base class an moving down. For subtype relationships that form a directed acyclic graph (DAG), multiple traits may expect their methods to be in the same slot. Thus, it may be necessary to use a separate trait table when accessing this type from a reference of the trait type. Thus when upcasting, the trait table pointer of the object reference will need to be changed. This can be easily supported by placing pointers to alternate trait tables for the type and negative offsets from the trait table pointer (i.e. before the pointer to the type object).

## Optimize to Value

The borrow checker will automatically provide escape analysis and will encourage a programming style where most objects do not escape. References that never escape do not need to be allocated on the heap, but can instead be allocated on the stack. Likewise, for owned references in objects that never escape. Rather than being allocated as a separate object on the heap, they can be allocated as part of the owing object. Not that these optimizations may occur in one function/type but not another.

## Generics

Generics in Adamant are a particularly complicated feature. As such it will require some interesting tricks to implement efficiently.

### Monomorphization

Monomorphization is the process of taking generic functions and creating separate versions of them for different generic parameters. This is often termed *reification*. The hope is that generics will be reified for value types, but that reference types will largely share a single implementation. There may be exceptions to this. For example, if whole program analysis determines that a generic class is only used with two different reference types, it may make sense to optimize by reifying the generic class for those two types.

### Bounded Generics

Given some class or function with a trait bound on its generic parameter (i.e. "`where T: Foo`"), how can these share a single implementation? The issue is that the fat pointer of the type "`T`" must be preserved in order to pass along references as "`T`". However, the vtable pointed to could place the methods of the trait in different slots depending on which type it is. Ideally, that would not be the case, but under complex subtype relationships, it may be unavoidable. In that case, it may be necessary to pass an additional argument to the function or constructor which is an offset in the trait table of type "`T`" for the trait table for use as the super type (i.e. the information for upcasting). The generic type would then have to lookup that trait table. Note however, that for any given instance of "`T`" the new trait table pointer could be cached so that multiple calls do not each suffer the extra overhead of looking up the trait table.

### Non-type Generic Parameters

Generic parameters that are not types may need to be passed as actual parameters at runtime. For example, the size of a fixed size array whose size is a generic parameter may need to be passed as a parameter to functions generic over the size of arrays. In some instances the compiler may determine that a small set of fixed values are used, or that a particular value should be optimized, but often it wouldn't make sense to reify an instance for every possible value of the generic parameter.

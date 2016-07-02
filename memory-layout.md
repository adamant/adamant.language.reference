# Memory Layout

Adamant is meant to be high performance with fixed, low overhead for common operations.  However, in many languages the support for interfaces or multiple inheritance can lead to lots of overhead and slow interface method dispatch.  Additionally, Adamant is unique in its support for implementing the interface of a class.  This means that a given method may be called with an actual instance of a class or with an unrelated class that is simply implementing its interface.  Effectively, this potentially transforms every method call into an interface method dispatch.

An additional wrinkle is added by allowing method implementation on interfaces.  In that case it is possible to end up in a diamond inheritance with two different implementations for a given method.  The language would then error on an ambiguous call to that method.  However, we must support calling the method after casting to one of the two interfaces.

The language does not specify memory layout and it is implementation defined.  The approaches described below are just two possible ways of implementing the language's semantics.

## Fat Pointers

All objects in memory are stored without a vtable or type descriptor pointer.  Instead pointers to objects are "fat" as needed.  Meaning pointers are actually a pair of a pointer to the data and a pointer to the vtable with the type descriptor.  Casting therefore involves changing the vtable portion of this.

Whenever the compiler is able to determine the concrete type of a value, it can use a regular pointer rather than a fat pointer.  Then if it calls a function needing a fat pointer, it can use a constant value for the vtable pointer.

Fat pointers risk increasing total memory usage when they are used in many collections.  Rather than a single vtable pointer stored in the object, a vtable pointer is stored with each reference to the object.

On the other hand, many objects can be stored without any vtable pointer at all.  Furthermore, the planned monomorphization optimizations play well with this.  

Note: Rust uses fat pointers for trait objects.  Swift uses 40 byte fat pointers for all protocol references. Go uses fat pointers for all interface references.

## VTables

Regular C++ virtual methods are put into a vtable.  However, supporting Adamant style interfaces that way would require every base class declaration to effectively be virtual inheritance.  Leading to significant impacts in performance.

Alternatively, an approach similar to that described in http://feepingcreature.github.io/oop.html would allow for a simpler system with less overhead for interface method calls.  However, since every method call is effectively an interface method call.  All vtables (even for the base class) would need to have an object offset.  Of course, the compiler could optimize it away in cases where it could prove it would never be needed.  That could be a viable approach.

Another vtable approach that could work is a method coloring approach.  Essentially, assign any given method a specific slot across all class vtables.  This can be done compactly using a graph coloring type algorithm.  That is, methods can share a slot if there is no class that implements them both.  However, it then becomes unclear how to implement the situation of interfaces with method implementations in a diamond inheritance situation.  The two methods override the same method, so they must be in the same slot, but there is only one vtable for the concrete class.  So what goes in the vtable in that case? Perhaps you could somehow outlaw this situation, for example by requiring the child class/interface to override the method and select the correct implementation for all instances of that type.  **That would be a good limit to add to the language regardless of which implementation approach is taken to prevent getting boxed in later.**
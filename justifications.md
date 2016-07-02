# Justifications

This document gives justifications for many of the design decisions of Adamant.  The reason this is useful is because it is so easy to forget why a certain design is necessary and begin to think of changing the design in a way that actually won't work.

## Principles

There are a number of design principles that are followed throughout Adamant and used as justifications for many design decisions.  This section defines those, rather than repeating their definitions below.

Allow for Future Expansions
: Design things in restrictive ways that allow for those restrictions to be lifted in the future if it makes sense.  Also, ensure syntax and semantics would be orthogonal to likely future enhancements to the language.

Be Explicit
: Require explicit declaration of things to provide clarity to the reader and a safety check.  For example, a class must be declared `abstract` if it has any abstract methods even though the compiler could just implicitly assume the class to be abstract in that case.

Best Practice Defaults
: If there is a clear best practice then that should be the "default" i.e. require no extra keyword or code.  For example, the default is that methods are overridable (unlike C++ or Java that require the `virtual` keyword). Another example is the default being immutable and making things mutable requiring the `mut` keyword.

Do Not Lock in an Implementation
: Make design choices that enable as many implementations as possible.  We don't want to lock Adamant into a particular approach to implementing the language that may have performance drawbacks.

Only One Way
: Do not provide multiple equivalent ways to do something or express something.

Support Program Evolution
: Code changes over time, the language should make that process as smooth as possible.  For example, requiring explicit override or hide keywords allows for a clear handling of the situation where a method is added to a base class that matches a method that was previously present only on a subclass.

## Design Decisions


All Methods Must Have a Unique Implementation
: Given a method `M` in interface `A` and interfaces `B` and `C` that implement `A` and override `M`, a class `D` that implements `B` and `C` must override `M` to provide a unique implementation of `M` to be called.  This applies even if `B` and `C` rename `M`.  At first glance this seems a strange restriction to place.  However, since Adamant is trying to not lock in an implementation, this restriction ensures certain implementations are possible, and can be safely removed in the future if desired.  Specifically, it enables a vtable implementation where every method across all classes is assigned a unique slot and there is only one vtable pointer in an object.  If this restriction were not in place, it would then be ambiguous which method pointer to put in the `M` vtable slot for class `D`.

`internal` Access Modifier
: Several other keywords were considered instead of `internal` for that access modifier.  The internal keyword doesn't clearly indicate internal to what.  For a while `package` was used instead.  However, package reads as if one is actually declaring a package.  The synonyms of internal `limited` and `restricted` were also considered.  They have the same problem of not clearly indicating what unit they are relative to.  Given that, it seemed best to stay with `internal` for familiarity to C#.

New Operator
: Any value with mutable state should be created with the `new` operator. Rust doesn't have a new operator.  However, it was decided that for Adamant the explicitness of having a new operator for allocating memory was good.  It then might seem to make sense to not use new for structs.  Using new for structs in C# has always felt a little weird.  However, the optimizer of Adamant is expected to frequently convert reference types into stack allocations so they might end up equivalent.  Also, it make structs consistent.  And it would seem weird to declare struct constructors using new but then call them without new.  One can't just have static functions build structs because of safety around definite assignment.


No Python Style Loop Else
: Python's loop else condition runs after the loop as long as break was not used.  A feature like this was considered, though it would have needed a better keyword.  However, that construct is more useful when selecting an item out of a collection, and in Adamant this will be done with the `for` loop that.  Since `for` loops operate on iterators, using a filter on the iterator is a better and easier way to do this.  Also, it wasn't clear how to combine such a feature with the existing loop else feature.


Numeric Types
: Both Rust and Go use integer values to indicate the bit length of types.  However, C# did not and it was already planning for 64 bit architectures.
The numeric sizes were selected based on the following criteria
	1. Anything larger than 64 bit pointers is unlikely any time soon
	2. Old 32 bit machines could have 64 bit pointers
	3. We want to be friendly to embedded processors that might be special
	4. We want standard sizes to be nice
	5. Platform dependent sizes cause issues

The default sizes (i.e. those that leave off the bit length) were chosen as a compromise between small enough memory use and enough digits of precision to be useful.  Hence 32 bit integers with there more than 9 digits were chosen as the default integer size while 64 bits were chosen for floats and decimals because their 32 bit versions had only 7 digits of precision.


Value Types
: The `struct` keyword is used to define value types.  Originally, Adamant was not going to have value types, but only reference types declared with `class`.  This ran into several issues.

First, it was unclear how to interop types with C without complicated marshalling since all types could have a vtable.  There were also issues about subtyping since any type could be subclassed.  How would one handle a subclass of `int` being passed to an external function?  Still it would have been possible to apply the `external` keyword to class declarations and restrict external classes to have no base class and no subclasses.

Second, there were problems around combining move and copy semantics into reference types.  For example, consider a reference to a `var` containing a class with move semantics.  What if one were to assign a different instance into it that was actually a subclass.  Since they would be different sizes, that could never be implemented unless types with move semantics were still stored as references to the heap.  Of course, the compiler could probably optimize that away in many cases, but it seemed foolish to rely on that so much.

Third, in implementing low level functionality like reference counting it seemed really useful to have a type that could be certain to exist on the stack as a value type. 
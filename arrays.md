# Arrays

Array types are `baseType[N]` where N is a constant size or `baseType[]` which is really kind of a short hand for an existential type since we know it has a length, we just don't know what it is. Given everything is a reference, there shouldn't be array literals, however there should be array constructors using new. Possible syntax for constructing an array.

    new int[45](); // bugbug what about initializing this?
    new [1,2,3,4,5];
    new int[45]; // An array of one int element?

Array elements should be mutable. However the question becomes whether you can have much more complex array types. Can you say "an array of immutable references to mutable objects?"  Can you have an array of references to other variables (i.e ref). It may simplify things if those are disallowed. Those aspects are then not part of the type, but properties of the variable binding. If arrays are allowed to do those complex things, then so should lists be allowed.

Arrays as other collections have a `Count`. Length might apply but seems over specific and doesn't fit with the other collections. Size might imply size in bytes or even capacity. For example, the size of a box or other container isn't how many things are in it. But how many could fit in it. With those options rules out. Count seems the only reasonable choice

# Alternative: Non-primitive Arrays

What if arrays were not at all primitive but just another type. That fits well with how `List<T>` is the primary collection in C# and `Vec<T>` the primary collection in Rust.

Then the challenge is how to represent multi-dimensional arrays. The rank (i.e. number of dimensions) could be another type param. Then it becomes confusing how to write the methods that need to deal with the dimensions as separate params. Still, if we could do that in the type system, it would mean there was a really cool and powerful type system. Another question is, if rank is a type param, what about arrays of known exact size, can we easily handle them?

    public class Array<T, Rank:size=1>
    {
        public new<S...>(size: S...)
            where S.Count = Rank, S.* = size
        {
            // ...
        }

        public operator [](i: ????)
        {
            // ...
        }
    }

Alternatively, there could be a separate type for multi-dimensional arrays. Either one for each number of dimensions, or one for all multi-dimensional arrays. The latter would still require complex types but might simplify the basic array type.

    public class Array<T>
    {
    }

    public class MultiArray<T, Rank:size> : Array<T>
    {
        public operator [](i: ????)
        {
            // ...
        }
    }

Or,

    public class Array2<T> : Array<T>
    {
        public operator [](i: size, j: size)
        {
        }
    }

    public class Array3<T> : Array<T>
    {
    }

Then there could be overloads allowing arrays with fixed size.

    public Array<T, Size:size>
    {
    }

## Array Size as always a type parameter

Another option is to make the array size always a type parameter and support some special syntax in the language that means, I don't really know the size which will have to be passed as a parameter. For example, maybe:

    public Array<T, Size:size=*>
    {
    }

    public Function(x: size) -> void
    {
        let x: mut = new Array<byte, 5>(); // TODO would actually need to initialize
        let y: mut = new Array<byte, =x>(); // using a variable as a type parameter here?
    }

    public Sort<T>(array: mut Array<T>) -> void // the default for size is * which means it will be passed as a parameter
    {
        // ...
    }

Or
    public Sort<T, Size:size>(array: mut Array<T, Size>) -> void // this is really interesting, but could be annoying
    {
        // ...
    }

## Array Lifetimes

There are logically three lifetimes involved with arrays and array slices. They are:

    1. The lifetimes of the reference to the array/the array itself
    2. The lifetime of the block of memory holding the elements
    3. The lifetime of the references held in the array

Lifetime 1 is the lifetime of the reference and should work out ok in the language. Lifetime 3 should be tied to the type parameter somehow. It is lifetime 2 that is awkward. It should default to `own` but there needs to be a way to specify that it is different. Think about the array slice method. There are kind of three possible versions of it.


    // Slice of immutable, returns new slice whose contents are bounded (syntax?)
    public Slice(this, from: size, to: size) -> own Array<T>
    {
        return new Array //...
    }

    // Slice of mutable, returns new slice whose contents are bounded (syntax?)
    public Slice(mut this, from: size, to: size) -> own Array<own T>
    {
        return new Array // ..
    }

Imagine a syntax where lifetimes can be tied to generic parameters. The buffer type used to implement string and array could then be:

We imagine there are a number of types:

* own Buffer<own T>
* own Buffer<mut T>
* own Buffer<T> - T's are immutable
* mut Buffer<own T>
* mut Buffer<mut T>
* Buffer<T> - T's are immutable, here nothing but immut T would be allowed


    public class Buffer<T|, [Elements = [own]> // T| means T bounded by an un-named lifetime, |Elements means a lifetime named elements
    {
        private var capacity: size;
        private var elements: *mut T | Elements; // the lifetime of the pointer is bounded by |Elements, (what is the correct order here?)

        public new(capacity: size) //we allow capacity 0
        {
            self.capacity = capacity;
            // TODO here I have made up a strange syntax for allocating raw memory on the assumption that arrays aren't built in, and this will be rarely done
            // Maybe this should be rust style with a Heap type that has the alloc functions on it
            elements = capacity > 0 ? new *T(capacity) : null;
        }

        // TODO this constructor is strange because it needs to bound the lifetime of the new object with the lifetime of elements
        private new(capacity: size, elements: *mut T | Elements) // the lifetime of the elements parameter is the lifetime of |Elements
        {
            self.capacity = capacity;
            self.elements = elements;
        }

        public operator ||() -> size | self
        {
            return capacity;
        }

        public unsafe operator [](i: size) -> T | self // If T is mut, this will be disallowed because it would steal the value?
            requires i in 0..capacity // used in as a boolean operator here, notice we allow capacity 0
        {
            return elements[i];
        }

        public unsafe operator [](mut self, i: size) -> ref T | self // interestingly you could end up with something like a ref to a borrow with a lifetime
            requires i in 0..capacity // used in as a boolean operator here
        {
            return &elements[i]; // Use & for address of?
        }

        public unsafe Resize(mut self, capacity: size) -> void // unsafe because it could drop elements without calling their destructors
        {
            // TODO implement resize by adjusting the capacity and reallocating the elements
        }

        public safe Double(mut self) -> void
            requires capacity <= size.Max / 2
        {
            unsafe(self.Resize(capacity * 2));
        }

        public Reserve(mut self, capacity: size) -> void
        {
            if(self.capacity < capacity)
                self.Resize(capacity);
        }

        public Reserve(mut self, used: size, reserve: size) -> void
            requires used + reserve <= size.Max
        {
            self.Reserve(used + reserve);
        }

        public Slice(range: Range<size>) -> own Buffer<T, |Elements>
            requires range.Start in 0..capacity
            requires range.End in 0 to capacity
        {
            // TODO this is strange because elements should be `*const T` here, but we need to pass it to the constructor that takes `*mut T`
            return new Buffer(|range|, elements + range.start);
        }

        public Slice(mut self, range: Range<size>) -> own mut Buffer<T, |Elements> // TODO do we need the explicit mut here?
            requires range.Start in 0..capacity
            requires range.End in 0 to capacity
        {
            return new Buffer(|range|, elements + range.start);
        }
    }

---------------------  new idea

System.Runtime defines a type UnsafeArray<T> which represents a block of Ts that can be uninitialized and for which no bounds checking is performed. You can then borrow portions of it. Do to the fact that the compiler my optimize references out of existence and optimize values into the stack. And unsafe array could end up with any of the following representations:

    1. A reference to UnsafeArray<T> is a pointer to a block of pointers to T
    2. A reference to UnsafeArray<T> is a pointer to a block of T
    3. A reference to UnsafeArray<T> is a block of pointers to T
    4. A reference to UnsafeArray<T> is a block of T

Given that, it doesn't make sense to provide an implementation of UnsafeArray<T> in Adamant. But the API of UnsafeArray<T> is:

    public class UnsafeArray<T>
    {
        public new(cardinality: size);
        public unsafe operator[](i: size) -> ref T;
        public unsafe delete() -> void;
    }

Consider an `UnsafeArray<T>` named `a`. Now either:

    1. it mutably or immutably owns the Ts it contains (WHEN YOU TAKE OWNERSHIP, YOU CAN RECOVER MUTABILITY)
    2. it immutably borrows the Ts it contains
    3. it mutably borrows the Ts it contains
    //4. it references variables of the Ts it contains

Imagine the signature of each if we don't have ref

    // Case 1, Owns
    public class UnsafeArray<?>
    {
        public new(cardinality: size);
        public unsafe get[](self, i: size) -> T // returns borrow with implicit lifetime of self
        public unsafe get[](mut self, i: size) -> mut T // returns borrow with implicit lifetime of self
        public unsafe set[](mut self, i: size, value: move T) -> mut T // returns borrow with implicit lifetime of self
        public unsafe delete() -> void;
    }

    // Case 2, Immutable Borrow
    public class UnsafeArray<?>
    {
        public new(cardinality: size);
        public unsafe get[](self, i: size) -> 'a T // need lifetime here for all the borrows
        public unsafe set[](mut self, i: size, value: 'a T) -> 'a T // returns borrow with implicit lifetime of self
        public unsafe delete() -> void;
    }

    // Case 3, Mutable Borrow
    public class UnsafeArray<?>
    {
        public new(cardinality: size);
        public unsafe get[](self, i: size) -> 'a T // need lifetime here for all the borrows
        public unsafe get[](mut self, i: size) -> mut 'a T // need lifetime here for all the borrows
        public unsafe set[](i: size, value: 'a T) -> 'a T // returns borrow with implicit lifetime of self

        public unsafe operator[](i: size) -> ref 'a mut T // need lifetime here for all the borrows
        public unsafe delete() -> void;
    }


----------------------------------------

    public class UnsafeArray<T>
    {
        public new(cardinality: size);
        public unsafe get[](self, i: size) -> T // returns borrow with implicit lifetime of self
        public unsafe get[](mut self, i: size) -> mut T // returns borrow with implicit lifetime of self
        public unsafe set[](mut self, i: size, value: move mut T) -> mut T // returns borrow with implicit lifetime of self
        public unsafe offset(self, i: size) -> UnsafeArray<T> // returns borrow with implicit lifetime of self
        public unsafe offset(mut self, i: size) -> mut UnsafeArray<T> // returns borrow with implicit lifetime of self
        public unsafe delete() -> void;
    }

    let a: mut = new UnsafeArray<Foo>(); // owns Foos
    let b: mut = new UnsafeArray<borrow Foo>(); // borrow Foos
    let c: mut = new UnsafeArray<borrow mut Foo>(); // mut borrows Foos

    // If you imagine the second two substituted in the def you get

    // T = borrow Foo
    public class UnsafeArray
    {
        public new(cardinality: size);
        public unsafe get[](self, i: size) -> borrow Foo // returns borrow with implicit lifetime of self
        public unsafe get[](mut self, i: size) -> mut borrow Foo // returns borrow with implicit lifetime of self
        public unsafe set[](mut self, i: size, value: move mut borrow Foo) -> mut borrow Foo // returns borrow with implicit lifetime of self
        public unsafe delete() -> void;
    }

    // borrow Foo ~> Foo
    // mut borrow Foo ~> borrow Foo ~> Foo
    // move mut borrow Foo ~> move borrow Foo ~> borrow Foo ~> Foo

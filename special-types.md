# Special Types

The special types are primitive types that are not keywords and are instead defined in a pseudo-package. That is they appear to be defined in the package `adamant.language` even though they are actually primitive types. Within that package they are all declared in the namespace `adamant.language`. To reference them, one must use a fully qualified name or a using statement.

## `Unsafe_Array<T>`

Unsafe array is the "primitive" array type. It represents an array of elements and is unsafe because no checking is performed against the length of the array. This type acts as if it had the following class declaration.

    public mut struct Unsafe_Array<T>
    {
        private let data: *T;

        public new(length: size);
        public implicit new copy(value: ref Unsafe_Array<T>) {};

        // TODO account for get vs set, mutability and lifetimes.
        // TODO what is the correct syntax for all that?
        public unsafe operator [](index: size) -> T;
        public unsafe slice(index: size) -> Unsafe_Array<T>;
    }

Arrays are zero indexed. The indexing operator and slice method do not check that their arguments are within the bounds of the array. Passing arguments outside the bounds of the array results in undefined behavior.

The slice method returns a view into the array starting at the index. It does not take a second argument indicating the length or last index of the view because this argument could not be checked and would be unused.

Note: It should be possible to implement this type in Adamant, it is just that the language assumes it exists in certain cases.

**TODO:** maybe the language should assume *T and not require this type? Or just assume the type `adamant.language.Unsafe_Array<T>` is it. That way people not using the standard library could define it how they want?

## `optional<T>`

`optional<T>` is the underlying type of the optional type constructor `T?`. That is, `T?` is syntactic sugar for `optional<T>`. It is an error to directly use the `optional<T>` type in code. It is used only for extension and documentation purposes.

Note: It should be possible to implement this type in Adamant. It is just that the language provides special syntax for it.

**TODO:** Alternately, just define optional types as a translation to `adamant.language.optional<T>` and let that be implemented in some package.

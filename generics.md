# Generics

For value type `V` and reference type `R`, the type parameter `T` can be assigned any of:

  * `V`
  * `ref V`
  * `ref var V`
  * `R`
  * `mut R`
  * `ref var R`
  * `ref var mut R`

// TODO what about own?

Types can then be constructed from `T` in any of the following ways:

  * `T`
  * `ref T`
  * `ref var T`

Note that you can't specify the mutability of `T`. That is inherent to the type parameter.

To the code using type parameter `T`, it behaves have a value with move semantics and no copy function. This only changes if there are generic constraints.

## Constraints

    where T: class // Then `ref T` becomes invalid
    where T: struct
    where T: move struct
    where T: copy struct
    where T: copy() // T has a copy method, explicit or implicit. must be used explicitly though

## Type Lists

In some cases, it is useful to have generic parameters actually provide a list of types. The visitor pattern is one such situation. To use a type list, mark a type parameter as being of the type `type...`. This indicates it is a tuple of types. **Conceptually we have something like `class Foo<T: L> forSome L where L: Tuple`.**

    public class Something_Visitor<TArgs: type..., TReturn, TThrows: type...>
    {
        public Visit(host: Something, args: TArgs...) -> TReturn
            throws TThrows...
        {
        }
    }

The `...` operator causes the args to be gathered into a tuple and passed as a single tuple value.

Note: If `...` means to break apart the tuple into listed things, it seems like you should be able to call a function as `Func(tuple...)` and have it break apart the tuple. Note here, the generic types are passed like `new Something_Visitor<[int,int],int,[void]>()`.

## Variadic Generics

If one wants a type that allows multiple type arguments to be passed inline then do:

    public class My_Tuple<T...>
    {
    }

## Valued Template Parameters

Here we compute with type parameters.

    public Factorial<N: integer>() -> integer
    {
        return N*Factorial<N-1>();
    }

    public Factorial<0>() -> integer
    {
        return 1;
    }

Then with inlining and constant folding `let x = Factorial<10>()` could be evaluated at compile time.
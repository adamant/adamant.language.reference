# Generics

For value type `V` and reference type `R`, the type parameter `T` can be assigned any of:

* `V`
* `mut V`
* `ref V`
* `ref mut V`
* `ref var V`
* `ref var mut V`
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

To the code using type parameter `T`, it behaves as value type with move semantics and no copy function. This only changes if there are generic constraints.

## Constraints

```adamant
where T: class // Then `ref T` becomes invalid
where T: struct
where T: move struct
where T: copy struct
where T: copy() // T has a copy method, explicit or implicit. must be used explicitly though
```

## Type Lists

In some cases, it is useful to have generic parameters actually provide a list of types. The visitor pattern is one such situation. To use a type list, mark a type parameter as being of the type `type...`. This indicates it is a tuple of types. **Conceptually we have something like `class Foo[T: L] forSome L where L: Tuple`.**

```adamant
public class Something_Visitor[TArgs: type..., TReturn, TThrows: type...]
{
    public Visit(host: Something, args: TArgs...) -> TReturn
        throws TThrows...
    {
    }
}
```

The `...` operator causes the args to be gathered into a tuple and passed as a single tuple value.

Note: If `...` means to break apart the tuple into listed things, it seems like you should be able to call a function as `Func(tuple...)` and have it break apart the tuple. Note here, the generic types are passed like `new Something_Visitor[#[int,int],int,#[void]]()`.

## Variadic Generics

If one wants a type that allows multiple type arguments to be passed inline then do:

    public class My_Tuple[T...]
    {
    }

## Valued Template Parameters

Here we compute with type parameters.

    public Factorial[N: integer] -> integer
    {
        return N*Factorial<N-1>();
    }

    public Factorial[0] -> integer
    {
        return 1;
    }

Then with inlining and constant folding `let x = Factorial[10];` could be evaluated at compile time.

## Computing Types

Types can be computed at compile time. For example, given a type `Array[n: size, T]` one can declare a variable like so:

```adamant
let x = new Array[5.power(2), int]();
for i in 0..5.power(2)
{
    x[i] = i;
}
```

However, in such cases, the arguments need to be constant expressions. But, a generic argument should itself be considered to be known at compile time. So could it be used as well?

```adamant
public fn matrix[n: size]() -> Array[n.power(2), int]
{
    return new Array[n.power(2), int]();
}
```

This seems like it could easily lead to issues. What if the two expressions differed in some inconsequential way? For example what if one used `3-1` in place of the power `2`? Even though only pure functions are allowed it isn't obvious that the same value is being used twice. I think it makes sense to restrict them to use a shared calculation. This could be done with a special from of where.

```adamant
public fn matrix[n: size]() -> Array[n2, int]
    where let n2: size = n.power(2)
{
    return new Array[n2, int]();
}
```

It is now straight forward to type check this function without any code evaluation. Conceptually, the same thing could come up for non-generic functions.

```adamant
public fn matrix() -> Array[n, int]
    where let n: size = 5.power(2)
{
    return new Array[n, int]();
}
```

The compiler would thus treat each constant expression in a type position as a separate type. If one wanted to use the same type in multiple places, one would need to use a `where let` clause. Of course, defining which kinds of expressions require this could be awkward.

Compare https://github.com/rust-lang/rfcs/blob/master/text/2000-const-generics.md

There is also the issue of what one can know about the result of a function evaluating to a type.

```adamant
public fn make_array(n: size) -> Array[T]
    where T = compute_type()
{
    return (1..size).select(fn i => T.default()).to_array();
}

// TODO how to make promises about the type returned?
public fn compute_type() -> type
    ensures return <: Default
    // or does it need some form of `where let`?
{
    // ...
}

public trait Default
{
    public fn default() -> Self;
}
```

This same mechanism can be used to implement specialization. Assuming generics can only be overloaded on number of parameters then a `List[T]` could be specialized like so:

```adamant
public class List[T] <: List[T]
    where let T = bool
{
    // special implementation
}
```

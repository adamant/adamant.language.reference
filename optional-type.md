# Optional Type

The optional type is used to indicate situations where a value may not be present. It replaces the role of null pointers and null references in languages that support them. Handling of optional types is highly optimized and using an optional type should never be a performance concern.

## The `none` Value

To express that an optional type has no value, use the `none` keyword. Note, the type of `none` is `never?`

## Conditioning on a Value

**TODO:** Not sure the preferred way to do this. Listing options here.

```adamant
let x: int? = ...;

if let y? = x
{
    // y is the value of x
}

match x
{
    y? => ...,
    none => ...,
}

if x =/= none
{
    // should work, but no good way to work with value of x
}

if x? // an "is none" operator
{
    // x is not none
}
```

## Coalescing Operator

The coalescing operator `??` allows for the replacement of `none` with another value. If the expression to the left of the operator evaluates to a value other than `none` then the result of the coalescing operator is that value and the right hand expression is not evaluated. Otherwise, the result is the result of evaluating the right hand expression.

## Conditional Access

**TODO:** should there be C# style `x?.y` and `x?[y]` operators or should that be implicit.

## Operator Lifting

Operators are lifted for optional types similar to how they are in C#.

## Optional Types Precedence

The `Optional<T>` struct is immutable so `mut T?` must mean `Optional<mut T>`. But what does `ref V?` mean? Is it a reference to a variable of type `V?` or an option of a `ref V`? Phrased another way, is it `ref (V?)` or `(ref V)?` ?

```adamant
let x1: int? = none;
let y1 = ref x; // y: ref (int?)
var x2: int? = none;
let y2 = ref x2; // y: ref var (int?)

foo(x: ref x?) -> void
{
    // ...
}

foo(ref x1); // requires ref (int?)
foo(none); // requires (ref int)?
let z = 5;
foo(ref z) // requires (ref int)?
```

Notice that certain implicit conversions are safe.

| From                      | To                    |
| ------------------------- | --------------------- |
| `ref Optional<V>`         | `Optional<ref V>`     |
| `ref Optional<mut v>`     | `Optional<ref mut V>` |
| `ref var Optional<V>`     | not safe              |
| `ref var Optional<mut V>` | not safe              |

Given that, it makes sense to interpret optional types to be of the second form. On the other hand, it seems much more likely you will want a reference to a variable of type `T?` than an optional reference to a variable of type `T`. So, `mut` and `ref` have higher precedence than optional, but optional has higher precedence than `ref var`.  Thus for value type `V` and reference type `R` the optional types have the following meanings.

| Type             | Meaning                   |
| ---------------- | ------------------------- |
| `V?`             | `Optional<V>`             |
| `mut V?`         | `Optional<mut v>`         |
| `ref V?`         | `Optional<ref V>`         |
| `ref mut V?`     | `Optiona<ref mut V>`      |
| `ref var V?`     | `ref var Optional<V>`     |
| `ref var mut V?` | `ref var Optiona<mut V>`  |
| `R?`             | `Optional<R>`             |
| `mut R?`         | `Optional<mut R>`         |
| `ref var R?`     | `ref var Optional<R>`     |
| `ref var mut R?` | `ref var Optional<mut R>` |

**TODO:** What would be the syntax for `Optional<ref var V>`?

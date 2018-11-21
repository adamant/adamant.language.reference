## Optional Types

Optional types can have all values of an underlying type plus an additional value "`none`". The underlying type can be any type including value or a reference types.

```grammar
optional_type
    : type "?"
    ;
```

## The "`none`" Value

The special value "`none`" is used to represent when an optional type does not have a value. The value "`none`" has the type "`never?`" thus it can be assigned into any optional type.

## Conditioning on a Value

To conditionally operate on an optional value, use an "`if let`" expression. Other ways of checking for "`none`" are possible but not preferred.

```adamant
let x: int? = ...;

// Idiomatic way of checking for `none`
if let y? = x
{
    // y is the value of x
}

// Not Recommend
match x
{
    y? => ...,
    none => ...,
}

// Not Recommend
if x =/= none
{
    // Can't directly use the value of `x` in this block
}
```

## Coalescing Operator

The coalescing operator `??` allows for the replacement of `none` with another value. If the expression to the left of the operator evaluates to a value other than `none` then the result of the coalescing operator is that value and the right hand expression is not evaluated. Otherwise, the result is the result of evaluating the right hand expression.

## Conditional Access

Members of optional values can be accessed using the conditional access operator `x?.y`. This operator evaluates the left hand side. If the left hand side evaluates to `none`, then the right hand side is not evaluated and the result of the expression is `none`. Otherwise, the member is accessed and evaluated. Note that this is a short ciructing evaluation so that `x?.y.z()` would prevent the method `z` from being called if `x` were `none`, rather than simply evaluating `x?.y` to `none` and then attempting to call `z()` on it.

## Operator Lifting

Operators are lifted for optional types similar to how they are in C#.

## Optional Types Precedence

The optional type is immutable so `mut T?` must mean `(mut T)?`. But what does `ref V?` mean? Is it a reference to a variable of type `V?` or an option of a `ref V`? Phrased another way, is it `ref (V?)` or `(ref V)?` ?

```adamant
let x1: int? = none;
let y1 = ref x; // y: ref (int?)
var x2: int? = none;
let y2 = ref x2; // y2: ref var (int?)

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

| From               | To             |
| ------------------ | -------------- |
| `ref (V?)`         | `(ref V)?`     |
| `ref (mut V?)`     | `(ref mut V)?` |
| `ref var (V?)`     | not safe       |
| `ref var (mut V?)` | not safe       |

Given that, it makes sense to interpret optional types to be of the second form. On the other hand, it seems much more likely you will want a reference to a variable of type `T?` than an optional reference to a variable of type `T`. So, `mut` and `ref` have higher precedence than optional, but optional has higher precedence than `ref var`.  Thus for value type `V` and reference type `R` the optional types have the following meanings.

| Type             | Meaning            |
| ---------------- | ------------------ |
| `V?`             | `V?`               |
| `mut V?`         | `(mut V)?`         |
| `ref V?`         | `(ref V)?`         |
| `ref mut V?`     | `(ref mut V)?`     |
| `ref var V?`     | `ref var (V?)`     |
| `ref var mut V?` | `ref var (mut V?)` |
| `R?`             | `R?`               |
| `mut R?`         | `(mut R)?`         |
| `ref var R?`     | `ref var (R?)`     |
| `ref var mut R?` | `ref var (mut R?)` |

**TODO:** What would be the syntax for "`(ref var V)?`"?

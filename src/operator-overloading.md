# Operator Overloading

Operators are essentially methods with special names. They are declared with the `operator` keyword rather than the `fn` keyword. Some operators have semantics that don't map cleanly onto methods and require special handling. The following operators can be overloaded:

| Operators                               | Overloadability                                           |
| --------------------------------------- | --------------------------------------------------------- |
| `+` `-` `not` `^` `?`                   | These unary operators can be overloaded                   |
| `@` `=>`                                | These unary operators can't be overloaded                 |
| `+` `-` `*` `/` `..` `<..` `..<` `<..<` | These binary operators can be overloaded                  |
| `::` `.` `^.` `<:` `\|` `&`             | These binary operators can't be overloaded                |
| `==` `=/=` `<` `>` `<=` `>=`            | The comparison operators can be overloaded in pairs       |
| `and` `or`                              | These operators require special handling when overloading |
| `??`                                    | The coalescing operator requires special handling         |
| `+=` `-=` `*=` `/=` `%=`                | These operators can be overloaded for greater efficiency  |
| `=` `->` `f(x)` `as`                    | These operators can't be overloaded                       |

To make an operator overload generic, place the type parameters after the `operator` keyword.

For the below examples, these types will be used.

```adamant
public copy struct complex
{
    public let real: float;
    public let imaginary: float;

    public new(.real, .imaginary) { }
    public implicit new copy(other: ref Self) { }
}

public copy struct fuzzy_bool
{
    public let value: float;

    public new(.value)
        requires value >= 0 and value <=1
    {
    }
    public implicit new copy(other: ref Self) { }
}
```

Note: Operators are not tied to interfaces the way they are in Rust because that would imply that they have universal semantics. That is, that every use of the operator is meant to have the same semantics. However, just like method names, the same operator can have two different meanings in different contexts.

## Unary Operators

``` adamant
// In `complex`
public operator +(ref self) -> complex
{
    // return a copy, unchanged
    return self;
}

public operator -(ref self) -> complex
{
    return new complex(-real, -imaginary);
}

// In `fuzzy_bool`
public operator not(ref self) -> fuzzy_bool
{
    return new fuzzy_bool(1 - value);
}

// Has value operator. Can't be directly used, by is used for `?.`
public operator ?(self) -> bool
{
    return Value == 1;
}
```

Note: `?` does not have to return whether the value is `none`.

## Binary Operators

The binary operators can be overloaded.

```adamant
// In `complex`
public operator +(ref self, other: ref complex) -> complex
{
    return new complex(real + other.real, imaginary + other.imaginary);
}
```

In order to support operators whose left hand side is a primitive type. In binary operators, the self parameter may be the second parameter.

```adamant
// In `complex`
public operator +(ref self, other: float) -> complex
{
    return new complex(real + other, imaginary);
}

// In `complex`
public operator +(other: float, ref self) -> complex
{
    return new complex(other + real, imaginary);
}
```

## Comparison Operators

The comparison operators can be overloaded like other binary operators. However, they must overloaded in pairs. When one of the operators in pair is overloaded, the other one in the pair must be overloaded to.

| Operator | Pairs |
| -------- | ----- |
| `==`     | `=/=` |
| `>`      | `<`   |
| `>=`     | `<=`  |

For operators that have multiple syntax forms, these are all treated as equivalent. Note that the comparison operators cannot be overloaded for lifetime comparisons.

Note: The `obsolete` attribute can be used to mark a member of the pair as uncallable if it is explicitly desired not to implement the other member of the pair.

## Logical Operators

The logical operators `and` and `or` are special because they perform short circuit evaluation. That is, their second argument may not be evaluated. To support this, each requires two overloads. The first determines when the second argument isn't evaluated, the second provides the value when it is. The first must return an optional type. If the result is `none` then the second argument will be evaluated and the binary form called. The two overloads for an operator must be overloaded together. When overloaded for some type, `x and y` is effectively evaluated as `x.and() ?? x.and(y)` and `x or y` is effectively evaluated as `x.or() ?? x.or(y)`. Note the first argument will only be evaluated once.

**TODO:** one actually may want to transform the value before passing to the second function. Perhaps the result needs to be some other type that allows a value to be passed in the "none" case?

```adamant
// In `fuzzy_bool`
public operator[Other] and(self) -> fuzzy_bool?
    where Other: fuzzy_bool
{
    return if Value == 0 => self else => none;
}

public operator and(self, other: fuzzy_bool) -> fuzzy_bool
{
    return new fuzzy_bool(value.min(other.value));
}

public operator[Other] or(self) -> fuzzy_bool?
    where Other: fuzzy_bool
{
    return if value == 1 => self else => none;
}

public operator or(self, other: fuzzy_bool) -> fuzzy_bool
{
    return new fuzzy_bool(value.max(other.value));
}
```

Note: the generics are needed on the single argument form to allow overloading on the type of the second argument.

**TODO:** An alternative approach which might work better is to have the second argument be a closure or promise. That would rely on the compiler being able to optimize those though.

## Coalescing Operators

Similar to the logical operators, the coalescing operator is evaluated in two steps. The first step determines whether the second argument should be evaluated and the second step determines the coalesced value. The two overloads must be present together. The first form must return an optional type.

**TODO:** one actually may want to transform the value before passing to the second function. Perhaps the result needs to be some other type that allows a value to be passed in the "none" case?  In the code below, it would be the exception value.

```adamant
public enum struct Result[T, E]
    where E: Exception
{
    Ok(value: T),
    Error(exception: E);

    public operator[TOther] ??(ref self) -> Result[T, CompositeException]?
        where TOther: T
    {
        return match self
            {
                .Ok(v) => .Ok(v),
                .Error => none
            };
    }

    public operator ??(ref self, other: ref T) -> T
    {
        return other;
    }
}
```

## Assignment Operators

These operators will be automatically generated if the corresponding binary operator is overloaded. However, they can be directly overloaded for greater efficiency, different behavior or to mark them as uncallable.

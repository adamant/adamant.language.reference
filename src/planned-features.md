# Planned Features

To keep the first version of Adamant simple, a number of features have been omitted. However, to ensure that the language design will accommodate those features, they have been designed in advance. Those features are listed in this section.

Sections:

* [Types](#types)
* [Using Directives](#using-directives)
* [Aliases](#aliases)
* [Expressions](#expressions)
* [Operators](#operators)

## Types

### 128-bit Integer Types

Additional integer types for 128-bit integers.

```grammar
integer_type
    : ...
    | "int128"
    | "uint128"
    ;
```

### 128-bit Floating Point Type

Additional floating point type for IEEE 754 binary128 floating point math.

```grammar
floating_point_type
    : ...
    | "float128"
    ;
```

### Fixed Point Types

Fixed point types are an additional class of numeric types.

```grammar
numeric_type
    : ...
    | fixed_point_type
    ;

fixed_point_type
    : "fixed" [0-9]+ "." [0-9]+
    | "ufixed" [0-9]+ "." [0-9]+
    ;
```

Let *f* be the number of fractional bits, *m* be the number of magnitude or integer bits and *b* be the total number of bits. For *b*=*m*+*f* where *b*=8 or *b*=16 or *b*=32 or *b*=64 and *m* > 0, *f* > 0, the signed fixed point type is `fixed`*m*`.`*f* and the unsigned type is `ufixed`*m*.*f*.

Note: 128 bit fixed types are not supported because of issues with handling multiply.

### Decimal Types

Decimal types are an additional class of numeric types.

```grammar
numeric_type
    : ...
    | decimal_type
    ;

decimal_type
    : "decimal32"
    | "decimal"
    | "decimal128"
    ;
```

They provide IEEE 754 32-bit, 64-bit and 128-bit decimal numbers respectively (binary representation preferred).

### DEC64 Type

The "`system.math`" package defines the `dec64` type following the standard [dec64.org](http://dec64.org).

### Rational Type

The "`system.math`" package defines the `rational` type. An arbitrary precision rational either [integer/uinteger](https://en.wikipedia.org/wiki/Rational_data_type#Representation) or [A New Representation of the Rational Numbers
for Fast Easy Arithmetic](http://www.cs.toronto.edu/~hehner/ratno.pdf) by Eric C. R. Hehner and R. N. S. Horspool.

### Real Types

Arbitrary precision real number types could be very useful. However, their behavior for repeating an irrational numbers would need to be clarified (see Java `System.Numerics.BigDecimal`). It may also be useful to have types with a fixed number of fractional digits. These could be simple types or defined in the "`system.math`" namespace.

```grammar
numeric_type
    : ...
    | real_type
    ;

real_type
    : "real"
    | "real." [0-9]+
    ;
```

If these types are defined in the standard library, then "`real\.\d+`" could instead be "`real[f]`".

### Money Type

Although C# recommends using the "`decimal`" type for money because it exhibits decimal rounding, its underflow and overflow behavior is not ideal for money. Perhaps there should be a decimal type or types more suitable for money.

## Using Directives

### Global and Package Qualified Using Directives

A using directive can qualify the namespace with a package or the global namespace qualifier. This restricts the using statement to bring in names inside that package only or for the global namespace qualifier from the current package only. Package qualifiers are either the official package name or if an alias for the package is specified, the alias for the package. Note the dot after the double colon.

```adamant
using my.package::.foo.bar;
using ::.baz;
```

### Using Type Members

Allow using directives to name a type, in which case all associated functions of the type are brought into scope. The generic arguments are evaluated in the containing namespace, without any of the using statements in the current declaration being considered.

```adamant
using example1.Foo[int]; // This makes all associated functions of `Foo[int]` available
```

## Aliases

The keyword "`alias`" introduces an alias for a type name. It does not create a separate type.

```adamant
alias PromiseResult[T] = Promise[Result[T]]
```

Aliases can have any access modifier.

## Expressions

### Multiline String Literals

Multiline string literals start with three double quotes followed by only whitespace on the line. They continue until a line that begins with whitespace followed by three double quotes. The lines with the double quotes are not included. So the final line does not end with a line break. They can be indented. The indent of the first line is ignored, indentation beyond that is included in the string. Double quotes can be used in a multiline string. A line can be continued by ending it with a backslash. *Regardless of the line endings of the source file, lines in the string are terminated with '\n'.* a line can be continued using a backslash followed by a newline. Multiline string literals should also support string interpolation. (see [Swift Multiline String Literals](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html) for more information).

```adamant
let s = """
    Indent to the left of this is ignored
        This is indented one tab
    This line continues \
    onto the next line.
    """;
```

### Unchecked Expressions

Inside an unchecked expression, the basic operators on integer types would be unchecked. Thus overflow on "`+`", "`-`", "`*`" and "`/`" would not be checked. Note that wrapping on division can occur for `MIN / -1` on signed types. Because the result is `-MIN` which is too large of a positive value. Also note that unary "`-`" can likewise overflow.

Like unsafe, both expression and block forms would be allowed:

```adamant
let x = unchecked(y + z);

unchecked
{
    let a = b * c;
}
```

An unchecked expression is treated the same as an unsafe block. That is, you must now mark the function as "`safe`" or "`unsafe`". This supports the idea that unchecked math is used when you believe the wrapping won't occur but don't want checking to be done. When wrapping is expected, there are the "`wrapping_`*x*" functions. This is also why an unchecked expression makes more sense than individual unchecked operators. The wrapping methods should be used when wrapping is intended. Unchecked is used for performance. In that case, you don't want to accidentally miss an operator, you want a whole chunk of code to be unchecked. This is then easier to read.

Additionally, it might be possible to allow operator and function overloading on unchecked by using "`unchecked`" as a modifier on the function. This would allow non-primitive types to participate in unchecked contexts. For clarity, a "`checked`" modifier could be used as well. Both keywords are reserved.

One open question is what the spec should say about wrapping behavior in unchecked contexts. It could specify two's complement wrapping. However, there might be a performance benefit to leaving it more open. We wouldn't want the C style undefined behavior, but allowing different bit representations and segfaults might help for some hardware.

## Operators

### Operator Overloading for `?.` and `??` Operators

These operators should support overloading. It isn't clear exactly how this should work. Presumably, the behavior of the two operators should somehow be linked. It is an outstanding question whether there are every situations where the two operators need to be overloaded fully independently. As a general rule, having them independent would provide greater flexibility to developers who might find one or the other operators useful is a very different context.

The first step of both is to evaluate their left hand side operand. The result of which is either a valid value to continue executing with, or a "none" value. In the case of the `?.` operator the "none" value should be returned without evaluating the rest of the expression. In the case of the `??` operator the "none" value causes the right hand operand to be evaluated and its value used. It was thought this could be done by an overload of the "`?`" pseudo operator which returned an optional type. However, this does not allow the none value to be anything besides `none`.

The coalesce operator would require its own overload. This would either take as a parameter the left hand value or perhaps the "none" value returned by the first step of evaluation. This could be very useful. For example imagine coalescing two `Result[T, E]` values where both are errors. The first step would unwrap the error value from the left hand side. The second step would then combine this with the error value from the right hand side to produce a composite error. If this operator overload is fully independent from the `?.` then it may make sense to have apply the same approach as is used when overload `and`, and `or`.

## Logical Operator Overloading

The logical operators `and` and `or` are special because they perform short circuit evaluation. That is, their second argument may not be evaluated. Thus they can not be simple method calls. One approach would be to make the second argument a closure of the rest of the expression. However, the performance of that would need to be evaluated before that could be adopted. Another approach, similar to the one used by C# is to evaluate these operators in two steps. This approach is described below.

Each operator has two overloads. The first determines when the second argument isn't evaluated, the second provides the value when it is. The first must return an optional type. If the result is `none` then the second argument will be evaluated and the binary form called. The two overloads for an operator must be overloaded together. When overloaded for some type, `x and y` is effectively evaluated as `x.and() ?? x.and(y)` and `x or y` is effectively evaluated as `x.or() ?? x.or(y)`. Note the first argument will only be evaluated once. Not that for the `and` operator, the unary version returns either its "false" value or `none` when it is "true". Another challenge of this approach is that the unary version of the operator can't easily be overloaded based on the type of the right operand. In the example code below, this is handled using a type parameter. However, this is awkward without a type equality operator for the where clause. Even with one, it makes overloading have to account for constraint clauses.

An issue with this approach is that one may want to transform the value before passing it to the second overload. Perhaps the result needs to be some other type that allows a value to be passed in the "none" case?

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

## Member Access Operator Overloading

**TODO:* Define this

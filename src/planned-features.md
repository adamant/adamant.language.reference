# Planned Features

To keep the first version of Adamant simple, a number of features have been omitted. However, to ensure that the language design will accommodate those features, they have been designed in advance. Those features are listed in this section.

Sections:

* [Types](#types)
* [Using Directives](#using-directives)
* [Aliases](#aliases)

## Types

### 128-bit Integer Types

Additional integral types for 128-bit integers.

```grammar
integral_type
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

### "`dec64`" Type

The "`system.math`" package defines the `dec64` type following the standard [dec64.org](http://dec64.org).

### "`rational`" Type

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

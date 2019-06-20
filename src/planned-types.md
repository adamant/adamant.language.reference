## Additional Types

### 128-bit Integer Types

Additional integer types for signed and unsigned 128-bit integers.

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

Fixed point types are an additional class of simple numeric types.

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

Decimal types are an additional class of simple numeric types.

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

The "`system.math`" package will define the `dec64` type following the standard defined on [dec64.org](http://dec64.org).

### Rational Type

The "`system.math`" package will define the `rational` type. It is an arbitrary precision rational number. This may be represented either as [integer/uinteger](https://en.wikipedia.org/wiki/Rational_data_type#Representation) or the one described in [A New Representation of the Rational Numbers
for Fast Easy Arithmetic](http://www.cs.toronto.edu/~hehner/ratno.pdf) by Eric C. R. Hehner and R. N. S. Horspool.

### Real Types

Arbitrary precision real number types could be very useful. However, their behavior for representing an irrational numbers would need to be clarified. For an example of how this type might work, see the Java `System.Numerics.BigDecimal` type. It may also be useful to have types with a fixed number of fractional digits. These could be simple types or defined in the "`system.math`" namespace.

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

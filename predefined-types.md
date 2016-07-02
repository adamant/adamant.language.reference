# Predefined Types

The predefined types are those types that exist as part of the language.  They are not in the standard library and are available without referencing any package.  Operations on predefined types are inherent to the language and the compiler knows how to implement them.  However, the predefined types contain only the most basic of operations on them.  The standard library extends them with a lot of functionality.

There are two kinds of predefined types.  The predefined keyword types are keywords of the language.  Keyword types make up the majority of the predefined types, but there are also a small number of predefined package types.

## Predefined Keyword Types

The predefined keyword types are keywords, but also act as types.  When the keyword is used in code as a type, it acts as a fully qualified reference to the predefined type.  To declare an identifier with the same name as one of these, use an escaped name.  That is, prefix the name with a backtick.  For example, ```string`.  References of an escaped identifier matching a predefined keyword type will apply standard name resolution rules.  The predefined types are not declared in any namespace, including the global namespace, so an escaped identifier reference will never match to one of the predefined types.

### String

### Numeric Types

Numeric types follow the pattern of a type name followed by an integer number of bits.  The exception being that certain default sizes omit the bit length.  The language requires that certain sizes be supported, but all sizes are reserved words for future use or compiler specific extensions.

#### Integer Types

Bits | Signed | Unsigned
---- | ------ | --------	
8	 | int8   | byte
16	 | int16  | unit16 
32	 | int    | uint
64	 | int64  | unit64
128	 | int128 | unit128

(all intX... and unitX... are reserved words)

I> The `System.Numerics` package defines arbitrary precision integer types `integer` and `uinteger`.

#### Floating Point Types

Bits | Type     | Standard
---- | -------- | ------------------
32	 | float32  | IEEE 754 binary32
64	 | float    | IEEE 754 binary64
128	 | float128 | IEEE 754 binary128

(all floatX... are reserved words)

#### Fixed Point Types
X	  fixedN.M where X=N+M and X is one of 8,16,32,64 (note 128 not supported because of multiply)
X	  ufixedN.M where X=N+M and X is one of 8,16,32,64 (note 128 not supported because of multiply)

(all fixedN.M... and ufixedN.M are reserved words)

#### Decimal Types

Bits | Type       | Standard
---- | ---------- | -------------------
32	 | decimal32  | IEEE 754 decimal32 (binary representation preferred) 
64	 | decimal    | IEEE 754 decimal64 (binary representation preferred) 
128	 | decimal128 | IEEE 754 decimal128 (binary representation preferred)

(all decimalX... are reserved words)

I> The `System.Numerics` package defines the `dec64` type following the standard http:// dec64.org  It also defines `rational`. An arbitrary precision rational either [integer/integer](https://en.wikipedia.org/wiki/Rational_data_type#Representation) or [A NEW REPRESENTATION OF THE RATIONAL NUMBERS
FOR FAST EASY ARITHMETIC ](http://www.cs.toronto.edu/~hehner/ratno.pdf) by ERIC C. R. HEHNER and R. N. S. HORSPOOL.  

The `System.Numerics` package should also define:
real // arbitrary precision decimal see Java BigDecimal defined in System.Numerics
real.N // arbitrary precision with N digits to the right of decimal? better money type? defined in System.Numerics? Should be real<N>?

#### Platform Sized Types

size, (need type for difference of pointers, perhaps 'offset' or c style 'ptrDiff')

Working with Rust has shown that size and usize will end up used for things that aren't sizes.  So perhaps `int` should be the native machine size and iXX should be used for the others.  Likewise f32 and f64 could be used for floats and `float` would be the oin

#### Issues

Does a C# checked context throw for loss of fractional digits? I don't think it does.  That seems like it could be an issue in some situations.

The concern is that decimal still doesn't form a good presentation for money because loss of sig figs still isn't checked?

What about a [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system)?

## Predefined Package Types

The predefined package types are predefined types that are not keywords and are instead defined in a pseudo-package.  That is they appear to be defined in the package `Adamant.Predefined` even though they are actually predefined types.  Within that package they are all declared in the namespace `Adamant.Predefined`.  To reference them, one must use a fully qualified name or a using statement.

### UnsafeArray

### Maybe<T>?
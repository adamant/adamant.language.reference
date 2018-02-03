# Predefined Types

The predefined types are those types that exist as part of the language. They are not in the standard library and are available without referencing any package. Operations on predefined types are inherent to the language and the compiler knows how to implement them. However, the predefined types contain only the most basic of operations on them. The standard library extends them with a lot of functionality.

There are two kinds of predefined types. The predefined keyword types are keywords of the language. Keyword types make up the majority of the predefined types, but there are also a small number of predefined package types.

## Predefined Keyword Types

The predefined keyword types are keywords, but also act as types. When the keyword is used in code as a type, it acts as a fully qualified reference to the predefined type. To declare an identifier with the same name as one of these, use an escaped name. That is, prefix the name with a backtick, for example `` `string``. References of an escaped identifier matching a predefined keyword type will apply standard name resolution rules. The predefined types are not declared in any namespace, including the global namespace, so an escaped identifier reference will never match to one of the predefined types.

### `bool`

Has values `true` and `false`.

### `code_point`

A 32 bit type used for a Unicode code point. Code point literals are created with single quotes as `'c'` or `'♠'`.

### `string`

TODO need to determine how conversion to string works and how formatting works.

TODO determine literal string syntax. That is strings that don't support embedded expressions and don't require characters to be escaped.

A Unicode string encoded in UTF-8. String literals are created with double quotes `"Hello World!"`. Expressions can be embedded in strings using curly braces, for example `"x = {x_variable}"`. The escape codes are:

Code | Character
---- | ---------
`\"` | Double Quote
`\'` | Single Quote
`\\` | Backslash
`\{` | Left Curly Brace
`\n` | Newline
`\r` | Carriage Return
`\0` | Null
`\b` | Backspace
`\f` | Form Feed
`\t` | Horizontal Tab
`\v` | Vertical Tab

### Numeric Types

Numeric types follow the pattern of a type name followed by an integer number of bits. The exception being that certain default sizes omit the bit length. The language requires that certain sizes be supported, but all sizes are reserved words for future use or compiler specific extensions.

#### Integer Types

Bits | Signed	| Unsigned
---- | -------- | --------
8	 | `int8`	| `byte`
16	 | `int16`	| `unit16`
32	 | `int`	| `uint`
64	 | `int64`	| `unit64`
128	 | `int128`	| `unit128`

(all names matching the regular expressions `int\d+` or `unit\d+` are reserved words)

I> The `System.Numerics` package defines arbitrary precision integer types `integer` and `uinteger`.

#### Floating Point Types

Bits | Type			| Standard
---- | ------------ | ------------------
32	 | `float32`	| IEEE 754 binary32
64	 | `float`		| IEEE 754 binary64
128	 | `float128`	| IEEE 754 binary128

(all names matching the regular expression `float\d+` are reserved words)

#### Fixed Point Types

Let *f* be the number of fractional bits, *m* be the number of magnitude or integer bits and *b* be the total number of bits. For *b*=*m*+*f* where *b*=8 or *b*=16 or *b*=32 or *b*=64, the signed fixed point type is `fixed`*m*`.`*f* and the unsiged type is `ufixed`*m*.*f*.

(note 128 bit fixed types are not supported because of issues with handling multiply)

(all names matching the regular expressions `fixed\d+\.\d+` or `ufixed\d+\.\d+` are reserved words)

#### Decimal Types

Bits | Type			| Standard
---- | ------------ | -------------------
32	 | `decimal32`	| IEEE 754 decimal32 (binary representation preferred)
64	 | `decimal`	| IEEE 754 decimal64 (binary representation preferred)
128	 | `decimal128` | IEEE 754 decimal128 (binary representation preferred)

(all names matching the regular expression `decimal\d+` are reserved words)

I> The `System.Numerics` package defines the `dec64` type following the standard http://dec64.org It also defines `rational`. An arbitrary precision rational either [integer/integer](https://en.wikipedia.org/wiki/Rational_data_type#Representation) or [A New Representation of the Rational Numbers
for Fast Easy Arithmetic](http://www.cs.toronto.edu/~hehner/ratno.pdf) by Eric C. R. Hehner and R. N. S. Horspool.

The `System.Numerics` package should also define:
 * `real` - arbitrary precision decimal see Java BigDecimal defined in System.Numerics
 * `real.`*f* - arbitrary precision with *f* digits to the right of decimal. Should this be `real<`*f*`>`?

TODO should there be a numeric type better suited to money?

#### Platform Sized Types

 * `size`
 * `offset`

The bit size of these systems is system dependent. Size is an unsigned number large enough to hold the maximum size of an array on the system. The size type is used to index into collections. Offset is a signed number with the same number of bits as size used to represent differences between array indexes.

#### Issues

Does a C# checked context throw for loss of fractional digits? I don't think it does. That seems like it could be an issue in some situations.

The concern is that decimal still doesn't form a good presentation for money because loss of sig figs still isn't checked?

What about a [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system)?

## Predefined Package Types

The predefined package types are predefined types that are not keywords and are instead defined in a pseudo-package. That is they appear to be defined in the package `Adamant.Predefined` even though they are actually predefined types. Within that package they are all declared in the namespace `Adamant.Predefined`. To reference them, one must use a fully qualified name or a using statement.

### `Unsafe_Array<T>`

Unsafe array is the "primitive" array type. It represents an array of elements and is unsafe because not checking is performed against the length of the array. This type acts as if it had the following class declaration.

	public mut class Unsafe_Array<T>
	{
		public new(length: size);
		// TODO account for get vs set, mutability and lifetimes.
		// TODO what is the correct syntax for all that?
		public unsafe operator [](index: size) -> T;
		public unsafe Slice(index: size) -> Unsafe_Array<T>;
	}

Arrays are zero indexed. The indexing operator and slice method do not check there arguments are within the bounds of the array. Passing arguments outside the bounds of the array results in undefined behavior.

The slice method returns a view into the array starting at the index. It does not take a second argument indicating the length or last index of the view because this argument could not be checked and would be unused.

### `Optional<T>`

TODO should it be forbidden to directly reference `Optional<T>
`? Then it would only be available for extension and documentation purposes.

The `Optional<T>` is the underlying type of the optional type constructor `T?`.  That is, `T?` is syntactic sugar for `Optional<T>`.

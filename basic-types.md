# Basic Types

The basic types are keywords, but also act as types. When the keyword is used in code as a type, it acts as a fully qualified reference to the type. To declare an identifier with the same name as one of these, use an escaped name. That is, prefix the name with a backslash, for example ``\string``. References of an escaped identifier matching a basic type will apply standard name resolution rules. The predefined types are not declared in any namespace, including the global namespace, so an escaped identifier reference will never match to one of the basic types.

## `bool`

Has values `true` and `false`. The boolean operators `and`, `or`, and `xor` operate on them. The if and loop constructs expect their expressions to evaluate to booleans.

## Escape Sequences

Both the `string` and `code_point` literals support escape sequences. These are:

Code | Character
---- | ---------
`\"` | Double Quote
`\\` | Backslash
`\{` | Left Curly Brace
`\n` | Newline
`\r` | Carriage Return
`\0` | Null
`\b` | Backspace
`\f` | Form Feed
`\t` | Horizontal Tab
`\v` | Vertical Tab
`\u`*XXXX* | Unicode Code Point (*X* is a hex digit)
`\u(`*X*+`)` | Unicode Code Point (1 to 6 hex digits)

**TODO:** should `\x`*XX* be supported?

## `code_point`

A 32 bit type used for a Unicode code point. Code point literals are created with single quotes as `'c'` or `'♠'`. All the escape sequences are accepted. In addition, the following code point literals are accepted.

Code  | Character
----- | ---------
`'\'` | Backslash
`'''` | Single quote

**TODO:** Is special casing backslash and single quote worth it? Does it block some future use of `'''` for something else?

Note: it doesn't make sense to allow interpolation in code point literals because the expression would result in the whole value. Furthermore, having verbatim code point literals would only simplify the backslash character.

## `string`

A Unicode string encoded in UTF-8.

### Interpolated String Literals

**TODO:** need to determine how conversion to string works and how formatting works.

String literals are created with double quotes `"Hello World"`. Expressions can be embedded in strings using curly braces, for example `"x = {x_variable}"`. This is referred to as interpolation. To avoid this, escape the left curly brace, for example `"Look Ma. \{ Braces }"`.

### Non-interpolated String Literals

To prevent interpolation in a string. Prefix it with the at sign. In non-interpolated string literals, left brace is not treated as a special character, but the `\{` and all other escape sequences are still accepted.

    let s = @"\t{no interpolation}";

### Verbatim String Literals

In verbatim string literals, neither string interpolation nor escape sequences are evaluated. To include double quotes use two double quote characters.

    let s = @@"Fully literal, {no interpolation} no escapes \n but ""double quotes""";

### Multiline String Literals

Multiline string literals start with three double quotes followed by only whitespace on the line. They can be made can be non-interpolated or verbatim as well. They continue until a line that begins with whitespace followed by three double quotes. The lines with the double quotes are not included. So the final line does not end with a line break. They can be indented. The indent of the first line is ignore, indentation beyond that is included in the string. Double quotes can be used in a multiline string. Exception in verbatim strings, a line can be continued by ending it with a backslash. *Regardless of the line endings of the source file, lines in the string are terminated with '\n'.*  (see [Swift Multiline String Literals](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html) for more information).

    let s = """
        Indent to the left of this is ignored
            This is indented one tab
        This line continues \
        onto the next line.
        """;

## Numeric Types

Numeric types follow the pattern of a type name followed by an integer number of bits. The exception being that certain default sizes omit the bit length. The language requires that certain sizes be supported, but all sizes are reserved words for future use or compiler specific extensions.

### Integer Types

Bits | Signed    | Unsigned
---- | -------- | --------
8     | `int8`    | `byte`
16     | `int16`    | `unit16`
32     | `int`    | `uint`
64     | `int64`    | `unit64`
128     | `int128`    | `unit128`

(all names matching the regular expressions `int\d+` or `unit\d+` are reserved words)

The `System.Numerics` package defines arbitrary precision integer types `integer` and `uinteger`.

### Floating Point Types

Bits | Type            | Standard
---- | ------------ | ------------------
32     | `float32`    | IEEE 754 binary32
64     | `float`        | IEEE 754 binary64
128     | `float128`    | IEEE 754 binary128

(all names matching the regular expression `float\d+` are reserved words)

### Fixed Point Types

Let *f* be the number of fractional bits, *m* be the number of magnitude or integer bits and *b* be the total number of bits. For *b*=*m*+*f* where *b*=8 or *b*=16 or *b*=32 or *b*=64 and *m* > 0, *f* > 0, the signed fixed point type is `fixed`*m*`.`*f* and the unsigned type is `ufixed`*m*.*f*.

Note: 128 bit fixed types are not supported because of issues with handling multiply.

(all names matching the regular expressions `fixed\d+\.\d+` or `ufixed\d+\.\d+` are reserved words)

### Decimal Types

Bits | Type            | Standard
---- | ------------ | -------------------
32     | `decimal32`    | IEEE 754 decimal32 (binary representation preferred)
64     | `decimal`    | IEEE 754 decimal64 (binary representation preferred)
128     | `decimal128` | IEEE 754 decimal128 (binary representation preferred)

(all names matching the regular expression `decimal\d+` are reserved words)

The `System.Numerics` package defines the `dec64` type following the standard [dec64.org](http://dec64.org) It also defines `rational`. An arbitrary precision rational either [integer/uinteger](https://en.wikipedia.org/wiki/Rational_data_type#Representation) or [A New Representation of the Rational Numbers
for Fast Easy Arithmetic](http://www.cs.toronto.edu/~hehner/ratno.pdf) by Eric C. R. Hehner and R. N. S. Horspool.

The `System.Numerics` package should also define:

* `real` - arbitrary precision decimal (see Java `System.Numerics.BigDecimal`)
* `real.`*f* - arbitrary precision with *f* digits to the right of decimal. Should this be `real<`*f*`>`?

**TODO:** should there be a numeric type better suited to money?

### Platform Sized Types

* `size`
* `offset`

The bit sizes of these are is system dependent. Size is an unsigned number large enough to hold the maximum size of an array on the system. The size type is used to index into collections. Offset is a signed number with the same number of bits as size used to represent differences between array indexes.

**TODO:** That means offset can only offset half the length of an array.

### Issues

Does a C# checked context throw for loss of fractional digits? I don't think it does. That seems like it could be an issue in some situations.

The concern is that decimal still doesn't form a good representation for money because loss of sig figs still isn't checked?

**TODO:** What about a [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system)?

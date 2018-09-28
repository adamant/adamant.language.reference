# Text Types

The text types are those types in addition to `string` which support text/string manipulation.

## `system.text.String_Builder`

Represents a mutable string value. This class makes it easy to construct string values.

## `system.text.unicode.code_point`

A 32 bit type used for a [Unicode Code Point](https://unicode.org/glossary/#code_point). Code points support user defined literals of a single character as `'c'` or `'♠'`. As defined in the Unicode standard, a code point is any value in the Unicode codespace. That is, the range of integers from 0 to 10FFFF<sub>16</sub>.

## `system.text.unicode.scalar_value`

A 32 but type used for a [Unicode Scalar Value](https://unicode.org/glossary/#unicode_scalar_value). Scalar values user defined literals of a single character as `'c'` or `'♠'`. As defined in the Unicode standard, a scalar value is any Unicode code point except high-surrogate and low-surrogate code points. In other words, the ranges of integers 0 to D7FF<sub>16</sub> and E000<sub>16</sub> to 10FFFF<sub>16</sub> inclusive.

## `system.text` Namespace

The text types are those types in addition to `String` which support text/string manipulation.

## `system.text.String`

The standard string type. This encodes strings in UTF-8 and supports string literal values.

## `system.text.String_Builder`

Represents a mutable string value. This class makes it easy to construct string values.

## `system.text.unicode.code_point`

A 32-bit type used for a [Unicode Code Point](https://unicode.org/glossary/#code_point). Code points support user literals of a single character such as "`'c'`" or "`'♠'`". As defined in the Unicode standard, a code point is any value in the Unicode codespace. That is, the range of integers from 0 to 0x10FFFF.

## `system.text.unicode.scalar_value`

A 32-bit type used for a [Unicode Scalar Value](https://unicode.org/glossary/#unicode_scalar_value). Scalar values support user literals of a single character such as "`'c'`" or "`'♠'`". As defined in the Unicode standard, a scalar value is any Unicode code point except high-surrogate and low-surrogate code points. In other words, the ranges of integers 0 to 0xD7FF and 0xE000 to 0x10FFFF inclusive.

## `system.text` Namespace

The text types are those types in addition to `String` which support text/string manipulation.

### Encoding

Following the principle of [UTF-8 Everywhere](http://utf8everywhere.org/), string are encoded in UTF-8 by default in memory and all other places. The encodings classes must be used to read and write in other encodings.

### Line Endings

To optimally support cross platform development, newlines are represented in code as simply `\n`. Libraries should convert them when needed for output (for example to the console). Text reading libraries should support the new lines types. The default is `Mixed` which can accept a mix of any of the standard newline forms and converts them to `\n`. When writing text, libraries should support the new line types. The default is line feed `\n`, but other types can be selected, including native.

### `system.text.String`

The standard string type. This encodes strings in UTF-8 and supports string literal values.

#### Lifetime Issues

There is a potential problem with strings and ownership. If a string is an array of bytes, then some string objects will "own" their array slice and need to delete it. But other strings won't really own their array slices, they will be parts of other strings. Those string will need to not delete their array slice. There is some question as to how this should work and how to express it. The Rust prototype demonstrates it should be possible to unify these cases since they are both essentially pointers. The question is how. It seems like there needs to be a lifetime parameter that can be either some lifetime or "own" that indicates how the string holds its array. Of course, then the question becomes how array slices really work. Do they have the same problem with their underlying memory?

### `system.text.String_Builder`

Represents a mutable string value. This class makes it easy to construct string values.

### `system.text.unicode.code_point`

A 32-bit type used for a [Unicode Code Point](https://unicode.org/glossary/#code_point). Code points support user literals of a single character such as "`'c'`" or "`'♠'`". As defined in the Unicode standard, a code point is any value in the Unicode codespace. That is, the range of integers from 0 to 0x10FFFF.

### `system.text.unicode.scalar_value`

A 32-bit type used for a [Unicode Scalar Value](https://unicode.org/glossary/#unicode_scalar_value). Scalar values support user literals of a single character such as "`'c'`" or "`'♠'`". As defined in the Unicode standard, a scalar value is any Unicode code point except high-surrogate and low-surrogate code points. In other words, the ranges of integers 0 to 0xD7FF and 0xE000 to 0x10FFFF inclusive.

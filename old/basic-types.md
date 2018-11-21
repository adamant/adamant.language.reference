# Basic Types

## Escape Sequences

Both the `string` and [user defined literals](user-defined-literals.md) support escape sequences. These are:

| Code         | Character                                |
| ------------ | ---------------------------------------- |
| `\"`         | Double Quote                             |
| `\'`         | Single Quote                             |
| `\\`         | Backslash                                |
| `\n`         | Newline                                  |
| `\r`         | Carriage Return                          |
| `\0`         | Null                                     |
| `\t`         | Horizontal Tab                           |
| `\u(`*X*+`)` | Unicode Scalar Value (1 to 6 hex digits) |

Other characters following a backslash (except the left parenthesis of an interpolated string expression) in a string or user defined literal are an error.

## `string`

A Unicode string encoded in UTF-8. String literals are created with double quotes `"Hello World"`. All the escape sequences are accepted.

### Multiline String Literals

Multiline string literals start with three double quotes followed by only whitespace on the line. They continue until a line that begins with whitespace followed by three double quotes. The lines with the double quotes are not included. So the final line does not end with a line break. They can be indented. The indent of the first line is ignored, indentation beyond that is included in the string. Double quotes can be used in a multiline string. A line can be continued by ending it with a backslash. *Regardless of the line endings of the source file, lines in the string are terminated with '\n'.*  (see [Swift Multiline String Literals](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html) for more information).

```adamant
let s = """
    Indent to the left of this is ignored
        This is indented one tab
    This line continues \
    onto the next line.
    """;
```

### Interpolated String Literals

**TODO:** need to determine how conversion to string works and how formatting works.

Expressions can be embedded in strings using a backslash and parenthesis, for example `"x = \(x_variable)"`. This is referred to as interpolation. This can also be done in multiline string literals.

### Verbatim String Literals

Verbatim string literals do not support escape sequences or interpolation. They are prefixed with a pound sign `#"Hello\Goodbye"`. To include a double quote in a verbatim string literal, use two double quotes (i.e. `#"A ""smart"" dog."`).

### Issues

Does a C# checked context throw for loss of fractional digits? I don't think it does. That seems like it could be an issue in some situations.

The concern is that decimal still doesn't form a good representation for money because loss of sig figs still isn't checked?

**TODO:** What about a [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system)?

## `any`

A variable of the type `any` can have any reference or value type placed in it.

**TODO:** Can we say `any` supports function types too?

Additionally, `@any` can be assigned from any pointer type.

**TODO:** Can `@x` where `x` is of a reference type return a value of type `@any` so we can compare reference equality?

## Types

`type` and `metatype` can these be pseudo references so the programmer doesn't need to worry about them? What about constructing generic types? If they are reference types, should the be capitalized?

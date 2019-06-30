## Literals

```grammar
literal
    : boolean_literal
    | integer_literal
    | real_literal
    | user_literal
    | string_literal
    | interpolated_string_literal
    ;
```

### Boolean Literals

There are two boolean literal values: "`true`" and "`false`". The type of a boolean literal is "`bool`".

```grammar
boolean_literal
    : "true"
    | "false"
    ;
```

### Integer Literals

A decimal integer may not start or end with a digit separator. Hexadecimal and binary integer literals may not end with a digit separator.
**TODO:** should digit separators allow normal non-breaking space?

```grammar
integer_literal
    : decimal_integer_literal
    | hexadecimal_integer_literal
    | binary_integer_literal
    ;

digit_separator
    : " "
    | \u(202F) // Narrow No-break Space
    ;

decimal_integer_literal
    : decimal_digit_or_separator+
    ;

decimal_digit_or_separator
    : [0-9]
    | digit_separator
    ;

hexadecimal_integer_literal
    : "0x" hexadecimal_digit_or_separator+
    ;

hexadecimal_digit_or_separator
    : [0-9a-fA-F]
    | digit_separator
    ;

binary_integer_literal
    : "0b" binary_digit_or_separator+
    ;

binary_digit_or_separator
    : [0-1]
    | digit_separator
    ;
```

### Real Literals

A real literal cannot start or end with a separator. A separator cannot appear to either side of the decimal point, nor immediately before the exponent part nor start the exponent part.

```grammar
real_literal
    : decimal_digit_or_separator* "." decimal_digit_or_separator+ exponent_part?
    | decimal_digit_or_separator+ exponent_part?
    ;

exponent_part
    : ("e"|"E") sign? decimal_digit_or_separator+
    ;

sign
    : "+"
    | "-"
    ;
```

### User Literals

User literals allow for literal values for user defined types. They are enclosed in single quotes and allow character escape sequences for special characters. Examples include "`'c'`", "`'♠'`", "`'2018-09-28'`" or "`'c29a3471-ea8d-40e3-bb2b-ef563687f'`".

```grammar
user_literal
    : "'" character* "'"
    ;

character
    : single_character
    | simple_escape_sequence
    | unicode_escape_sequence
    ;

single_character
    : ?Any character except single quote (U+0027), backslash (U+005C), and new_line_characters?
    ;

simple_escape_sequence
    : "\"? ["'nr0t\\]
    ;

unicode_escape_sequence
    : "\u(" [0-9a-f-A-F]{1,6} ")"
    ;
```

| Code         | Character                                |
| ------------ | ---------------------------------------- |
| `\"`         | Double Quote U+0022                      |
| `\'`         | Single Quote U+0027                      |
| `\\`         | Backslash U+005C                         |
| `\n`         | Newline U+000A                           |
| `\r`         | Carriage Return U+000D                   |
| `\0`         | Null U+0000                              |
| `\t`         | Horizontal Tab U+0009                    |
| `\u(`*X*+`)` | Unicode Scalar Value (1 to 6 hex digits) |

Other characters following a backslash (except the left parenthesis of an interpolated string expression) in a string or user literal are an error.

#### User Literal Construction

User literals are constructed with an operator overload. The resulting object must be read only and have the static lifetime. This operator must be an implicit pure function.

```adamant
public struct Example
{
    public let value: string;

    public init(.value) { }

    public implicit operator '_'(value: string) -> Example$forever
    {
        return Example(value);
    }
}
```

#### Type Determination

Which type a user literal is for is determined by type inference. This means one can declare the type of a variable to force what literal will be constructed. However, this process can be further limited by filtering.

#### Requiring match

To filter which literal type a user literal might match, add preconditions to the operator overload. These should not filter to only the allowed values, but to a set of values that a developer might reasonably think could match.

```adamant
public struct Date
{
    public implicit operator '_'(value: string) -> Date$forever
        requires value.matches(#"\d+-\d+-\d+")
    {
        // construct date
    }
}
```

### String Literals

**TODO:** Change to delimited string literals the same as Swift. However, that may be a planned feature rather than a current feature.

String literals are Unicode strings encoded in UTF-8. They are enclosed in double quotes. There are two kinds of string literals. Regular string literals allow the same escape sequences as user defined literals. Verbatim string literals do not allow escape sequences. Similar to user defined literals, the data type of string literals is not fixed. Instead it is inferred to be a type with the appropriate operator overload.

```grammar
string_literal
    : regular_string_literal
    | verbatim_string_literal
    ;

regular_string_literal
    : ["] regular_string_character* ["]
    ;

regular_string_character
    : single_regular_string_character
    | simple_escape_sequence
    | unicode_escape_sequence
    ;

single_regular_string_character
    : ?Any character except double quote (U+0022), backslash (U+005C), and new_line_characters?
    ;

verbatim_string_literal
    : "#" ["] verbatim_string_character* ["]
    ;

verbatim_string_character
    : single_verbatim_string_character
    | quote_escape_sequence
    ;

single_verbatim_string_character
    : [^"]
    ;

quote_escape_sequence
    : ["]{2}
    ;
```

**TODO:** verbatim strings could conflict with future multiline verbatim strings `#""" string """`.

#### String Literal Construction

String literals are constructed with an operator overload. The resulting object must be read only and have the static lifetime. This operator must be an implicit pure function.

```adamant
public struct Example
{
    public let count: size;
    public let bytes: @byte;

    public init(.value, .bytes) { }

    public implicit operator "_"(count: size, bytes: @byte) -> Example$forever
    {
        return Example(count, bytes);
    }
}
```

### Interpolated Strings

Interpolated string literals are distinguished from regular string literals only by have interpolated expressions in them. An interpolated expression is enclosed by "`\()`". The interpolated expression can contain a string literal, but can’t contain an unescaped backslash, a carriage return, or a line feed.

```grammar
interpolated_string_literal
    : ["] interpolated_string_item* ["]
    ;

interpolated_string_item
    : "\(" expression ")"
    | regular_string_character+
    ;
```

**TODO:** determine how conversion to string works for interpolated string.
**TODO:** determine how formatting of the expression works.

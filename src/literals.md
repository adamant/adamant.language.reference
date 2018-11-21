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
    | <Unicode Narrow No-break Space (U+202F)>
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

### User Defined Literals

User defined literals allow for literal values for user defined types. They are enclosed in single quotes and allow character escape sequences for special characters. Examples include "`'c'`", "`'♠'`", "`'2018-09-28'`" or "`'c29a3471-ea8d-40e3-bb2b-ef563687f'`".

```grammar
character_literal
    : "'" character "'"
    ;

character
    : single_character
    | simple_escape_sequence
    | unicode_escape_sequence
    ;

single_character
    : <Any character except single quote (U+0027), backslash (U+005C), and new_line_characters>
    ;

simple_escape_sequence
    : <Backslash (U+005C)> (["'nr0t] | <Backslash (U+005C)>)
    ;

hexadecimal_escape_sequence
    : <Backslash (U+005C)> "(" [0-9a-f-A-F]{1,6} ")";
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

Other characters following a backslash (except the left parenthesis of an interpolated string expression) in a string or user defined literal are an error.

#### Literal Constructor

User defined literals are constructed with a special constructor. The resulting object must be read only and has the static lifetime. This constructor must be an implicit meta function and has the special name `''`. If it is a `struct` constructor, it must be an `init` function.

```adamant
public struct Example
{
    public let value: string;

    public implicit init ''[value: string]
    {
        self.value = value;
    }
}
```

#### Type Determination

Which type a user defined literal is for is determined by type inference. This means one can declare the type of a variable to force what literal will be constructed. However, this process can be further limited by filtering.

#### Requiring match

To filter which literal constructors a user defined literal might match, add preconditions to the constructors. These should not filter to only the allowed values, but to a set of values that a developer might reasonably think could match.

```adamant
public struct Date
{
    public implicit init ''[value: string]
        requires value.matches(#"\d+-\d+-\d+")
    {
        // construct date
    }
}
```

### String Literals

### Interpolated String Literals

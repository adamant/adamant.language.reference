## Literals

Literals is a representation of a value in source code. A portion of literals are simply keywords.

```grammar
literal
    : boolean_literal
    | integer_literal
    | real_literal
    | user_literal
    | string_literal
    | none_literal
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

Integer literals represent integer values for types like `int` and `unit`. There are no negative integer literals. Instead, negative values are represented by constant expressions using the negation operator on an integer literal. Integer literals can be written in decimal, hexadecimal, or binary.

Digits within an integer literal can be grouped using digits separators. This is similar to how comma can be used to group digits into groups of three. In Adamant, the separator may be an underscore or narrow non-breaking space. Integer literals allow the digits separators to be used to group digits in any way the developer wants. However, an integer literal may not start or end with a digit separator. Nor may they contain consecutive digit separators. For hexadecimal and binary integer literals, a digit separator may separate the prefix from the value.

Decimal integer literals may not start with the digit zero except for the value "0".

```grammar
integer_literal
    : decimal_integer_literal
    | hexadecimal_integer_literal
    | binary_integer_literal
    ;

digit_separator
    : "_"
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

Integer literals do not in of themselves determine the type for the value. Instead, they represent arbitrary precision integer constants which provide a value for integer types inferred by the context.

### Real Literals

Real literals represent floating point values for types like `float` and `float32`. Like integer literals, digit separators can be used in real literals to group digits. The integer portion, decimal portion and exponent may not start or end with a digit separator. This implies separator cannot appear to either side of the decimal point, nor to either side of the letter "e" which introduces the exponent. A real literal also may not contain consecutive digit separators. A the integer and exponent portions of a real literal may not begin with a zero digit except for when the integer portion is the value "0".

```grammar
real_literal
    : decimal_digit_or_separator+ "." decimal_digit_or_separator+ exponent_part?
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

Note: A real literal requires a digit before the decimal sign even if this is only the value zero. Also, if present, a decimal point must be followed by at least one digit.

### Escape Sequences

Both user literals and string literals may contain escape sequences. This allows them to contain characters that would otherwise be difficult to represent. Each escape sequence represents a single Unicode code point. Simple escape sequences provide for some common characters one may wish to escape. Escape sequences begin with a backslash which may be followed by a delimiter. The escaped value is determined by the character(s) following the backslash or delimiter. See the relevant literal section for a discussion of delimiters. Characters following a backslash or delimiter besides those documented below or the left parenthesis are an error. Interpolated string segments are another kind of escape sequence which are introduced by a left parenthesis following the backslash or delimiter.

Unicode escape sequences allow the escaping of any Unicode code point. They do this by giving the hexadecimal value of the code point as one to six hexadecimal digits. It is an error for a Unicode escape sequence to refer to a surrogate pair or a code point outside of those allowed by Unicode.

```grammar
escape_sequence
    : simple_escape_sequence
    | unicode_escape_sequence
    ;

simple_escape_sequence
    : "\" delimiter ["]  // Double Quote U+0022
    | "\" delimiter "'"     // Single Quote U+0027
    | "\" delimiter "\"     // Backslash U+005C
    | "\" delimiter "n"     // Newline U+000A
    | "\" delimiter "r"     // Carriage Return U+000D
    | "\" delimiter "0"     // Null U+0000
    | "\" delimiter "t"     // Horizontal Tab U+0009
    ;

unicode_escape_sequence
    : "\" delimiter "u(" [0-9a-f-A-F]{1,6} ")"
    ;

delimiter
    : "#"*
    ;
```

### User Literals

User literals provide literal values for user defined types. They are enclosed in single quotes and may contain escape sequences. They may not contain interpolated segments. Examples of user literals include "`'c'`", "`'♠'`", "`'2018-09-28'`", and "`'c29a3471-ea8d-40e3-bb2b-ef563687f'`". The type of a user literal is determined from context and content using type inference and pattern matching. The user defined type is then constructed from the string value of the user defined literal.

To enable user literals containing backslash, single quote, or other characters requiring escape sequences to be more easily written, they may be delimited. For example, this allows user literals to be used for regular expressions which use backslash for escaping characters. A delimiter consists of one or more pound signs immediately before and after the user literal. A delimited user literal is terminated by a single quote followed by the same number of pound signs as it was prefixed by. A single quote followed by fewer pound signs is taken to be part of the user literal value. A single quote followed by more pound signs terminates the user literal and is an error. Within a delimited user literal, escape sequences are only matched when they contain the same delimiter. That is when the backslash is followed by the same number of pound signs. A backslash followed by fewer or more pound signs is taken to be part of the user literal value.

```grammar
user_literal
    : delimiter "'" user_literal_character* "'" delimiter
    ;

user_literal_character
    : escape_sequence
      // any character except newline characters
    | [^] - newline
    ;
```

#### User Literal Construction

User literal values are constructed by calling the user literal operator. The resulting object must be read only and have the lifetime `forever`. This operator must be an implicit pure function.

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

Which type a user literal is for is determined by type inference. This means one can declare the type of a variable to force what literal will be constructed. However, this process can be further restricted by filtering.

#### Restricting User Literal Values

The values a user literal can have may be restricted to match a pattern. Types whose pattern does not match the string value of the user literal will not be considered during type inference. To restrict values, add preconditions to the operator overload. It is recommended that these preconditions not restrict the value to only the legal values. Instead, a more liberal pattern should be used and an exception thrown for illegal values. This will allow type inference to select the type a developer probably intended when writing an invalid value and correctly report an error.

```adamant
public copy struct Date
{
    public implicit operator '_'(value: string) -> Date
        requires value.matches(#"\d+-\d+-\d+"#)
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

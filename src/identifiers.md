## Identifiers

Identifiers are used as the names of types, variables, functions etc. in Adamant programs. There are three kind of identifiers. *Simple identifiers* are identifiers whose names are unambiguous in the Adamant syntax. In many languages, these are the only kinds of identifiers. *Escaped identifiers* allow the use of keywords and numbers as identifiers. Finally, *identifier strings* allow arbitrary text to be used as an identifier. When determining what a name refers to, the kind of identifier is not used, only the value. For example, a simple identifier and an identifier string with the same value are the same identifier. Additionally, identifier values are converted to Normalization Form C (NFC) before comparison.

```grammar
identifier
    : simple_identifier
    | escaped_identifier
    | identifier_string
    ;
```

### Simple Identifiers

Simple identifiers start with a letter or underscore and may be followed by a letters, underscores, and digits. However, a reserved word or keyword may not be used as a simple identifier. Additionally, all identifiers containing double underscore characters are reserved for use by the implementation and may not be used as simple identifiers. Identifier strings can be used to create an identifier containing double underscore.

```grammar
simple_identifier
    : identifier_or_keyword - keyword - reserved_word - reserved_identifier
    ;

identifier_or_keyword
    : identifier_start_character identifier_part_character*
    ;

reserved_identifier
    : identifier_part_character* "__" identifier_part_character*
    ;

identifier_start_character
    : letter_character
    | '_'
    ;

identifier_part_character
    : letter_character
    | decimal_digit_character
    | connecting_character
    | combining_character
    | formatting_character
    ;

letter_character
    : \p{L}  // Unicode Letter
    | \p{Nl} // Unicode Number, letter
    ;

combining_character
    : \p{Mn} // Unicode Mark, nonspacing
    | \p{Mc} // Unicode Mark, spacing combining
    ;

decimal_digit_character
    : \p{Nd} // Unicode Number, decimal digit
    ;

connecting_character
    : \p{Pc} // Unicode Punctuation, connector
    ;

formatting_character
    : \p{Cf} // Unicode Other, format
    ;
```

### Escaped Identifiers

A backslash can be used to escape keywords, reserved words, and names starting with digits for use as identifiers. For example "`\class`", allows one to use the "`class`" keyword as a variable name. Only keywords, reserved words and names starting with digits are allowed to be used as escaped identifers. For example, "`\hello`" is a nonfatal syntax error because "hello" is not a keyword. Escaped identifiers are used to access the fields of a tuple. For example, `t.\1` is the first field of the tuple `t`.

```grammar
escaped_identifier
    : "\" keyword
    | "\" reserved_word
    | "\" decimal_digit_character identifier_part_character*
    ;
```

For example, this code declares a variable using an escaped identifer and uses it.

```adamant
let \class = "Mammalia";
let order = "Carnivora";
let family = "Canidae";
let genius = "Canis";
let species = "lupus";

console.WriteLine("The Gray Wolf is \(\class) \(order) \(family) \(genius) \(species)");
```

### Identifier Strings

For more flexibility in variable names, strings can be used as identifiers names by escaping them with a backslash. This is particularly useful for tests.

```adamant
#Test
public \"`foo()` returns `true`"() -> void
{
    assert(foo());
}
```

Identifier strings are like verbatim strings. Escape sequences are not evaluated and values are not interpolated. To include double quotes in an identifer string use two double quotes. Newlines are not permitted in identifier strings.

```adamant
let \"He said ""Hello"" to me." = true;
```

It is an error to use a identifier string when it isn't necessary.

```adamant
let x = 1;
let y = \"x"; // error: `\"x"` is a legal identifer, use `x` instead
let \"class" = 5; // error: `\"class"` is a keyword identifier, use `\class` instead
```

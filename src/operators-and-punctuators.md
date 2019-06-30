## Operators and Punctuators

Operators and punctuators are symbols used in Adamant as operators and other symbolic connectors. Some symbols are used in both capacities. As a consequence, the division into operator and punctuator categories is somewhat arbitrary and is done for convenience only. Note that the lexical category of operator does not include everything that is semantically an operator. For example, the `and` and `or` keywords are operators.

```grammar
operator_or_punctuator
    : operator
    | punctuator
    ;
```

### Operators

The following symbols are used as operators.

```grammar
operator
    : "."
    | ".."
    | "<.."
    | "..<"
    | "<..<"
    | "@"
    | "^"
    | "^."
    | "+"
    | "-"
    | "*"
    | "/"
    | "="
    | "=="
    | "=/="
    | "≠"  // U+2260
    | "<"
    | "<="
    | "≤"  // U+2264
    | "</="
    | ">"
    | ">="
    | "≥"  // U+2265
    | ">/="
    | "+="
    | "-="
    | "*="
    | "/="
    | "??"
    | "?."
    | "<:"
    | "=>"
    | "|"
    | "&"
    ;
```

### Punctuators

The following symbols are used as symbolic connectors.

```grammar
punctuator
    : "$"
    | "::"
    | "?"
    | "{"
    | "}"
    | "("
    | ")"
    | "["
    | "]"
    | ";"
    | ","
    | "#("
    | "#["
    | "#{"
    | ":"
    | "->"
    ;
```

### Other Symbols

Any symbol sequences not defined as operators or punctuators are errors. Thus these sequences are not technically reserved, but are available for future language features. However, the following symbols are "reserved" for the below uses:

| Symbol  | Use                                      |
| ------- | ---------------------------------------- |
| `**`    | Raise to power                           |
| `` ` `` | Reserved for code expressions and blocks |
| `##`    | Preprocessor                             |

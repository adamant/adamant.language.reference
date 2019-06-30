# Operators and Punctuators

```grammar
operator_or_punctuator
    : operator
    | punctuator
    ;
```

## Operators

```grammar
operator
    : "."
    | "::"
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

## Punctuators

```grammar
punctuator
    : "$"
    | "?"
    | "{"
    | "}"
    | "("
    | ")"
    | "["
    | "]"
    | ";"
    | ","
    | "#"
    | ":"
    | "->"
    | "\"
    ;
```

## Other Symbols

Symbols not already defined are not technically reserved. They are simply invalid character sequences in Adamant. However, the following symbols are "reserved" for the below uses:

| Symbol  | Use                           |
| ------- | ----------------------------- |
| `**`    | Raise to power.               |
| `` ` `` | Reserved for use in Markdown. |
| `##`    | Preprocessor                  |

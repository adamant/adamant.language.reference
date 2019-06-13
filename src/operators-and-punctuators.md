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

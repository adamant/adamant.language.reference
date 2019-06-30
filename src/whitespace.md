## Whitespace

Whitespace is not significant except in that it may separate tokens. Whitespace includes all code points in the Unicode class Zs which includes the standard space character (U+20).

```grammar
whitespace
    : \p{Zs}   // Unicode whitespace class (Zs)
    | \u(0009) // Horizontal tab
    | \u(000B) // Vertical tab
    | \u(000C) // Form feed character
    ;
```

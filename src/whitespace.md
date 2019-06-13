## Whitespace

```grammar
whitespace
    : ?Any character with Unicode class Zs?
    | \u(0009) // Horizontal tab
    | \u(000B) // Vertical tab
    | \u(000C) // Form feed character
    ;
```

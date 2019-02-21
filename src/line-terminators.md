## Line Terminators

```grammar
new_line
    : ?Carriage return character (U+000D)?
    | ?Line feed character (U+000A)?
    | ?Carriage return character (U+000D) followed by line feed character (U+000A)?
    | ?Next line character (U+0085)?
    | ?Line separator character (U+2028)?
    | ?Paragraph separator character (U+2029)?
    ;
```

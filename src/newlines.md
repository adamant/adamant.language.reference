## Newlines

Newlines divide an Adamant program into lines. For the purposes of viewing a source file as a sequences of lines, the end of file terminates the last line.

```grammar
newline
    : \u(000D)         // Carriage return
    | \u(000A)         // Line feed
    | \u(000D)\u(000A) // Carriage return, line feed
    | \u(0085)         // Next line
    | \u(2028)         // Line separator
    | \u(2029)         // Paragraph separator
    ;
```

## Lexical Analysis

```grammar
input
    : input_element*
    ;

input_element
    : new_line
    | whitespace
    | comment
    | token
    ;
```

For comments, see [Comments](comments.md).

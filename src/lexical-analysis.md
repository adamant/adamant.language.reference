## Lexical Analysis

Every Adamant source file is composed of code points from the Unicode standard encoded in UTF-8. The terminal symbols of the lexical grammar are the Unicode Code points. The lexical grammar specifies how these code points are combined to form [newlines](line-terminators.md), [whitespace](whitespace.md), [comments](comments.md), and [tokens](tokens.md).

Every source file in an Adamant program must match the `input` production of the lexical grammar.

```grammar
input
    : input_element*
    ;

input_element
    : newline
    | whitespace
    | comment
    | token
    ;
```

Note: the current version of Adamant does not have a preprocessor. When one is added, it will be handled similarly to the C# preprocessor by including it in the lexical grammar.

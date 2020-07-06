## Lexical Analysis

Every Adamant source file is composed of code points from the Unicode standard encoded in UTF-8. The terminal symbols of the lexical grammar are the Unicode code points. The lexical grammar specifies how these code points are combined to form [newlines](line-terminators.md), [whitespace](whitespace.md), [comments](comments.md), and [tokens](tokens.md).

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

Lexical processing consists of breaking down the file into a sequence of tokens that are the input to the syntactic analysis. Newlines, whitespace, and comments can separate tokens, but have no impact on the syntactic structure of programs, except for [documentation comments](documentation-comments.md).

When multiple lexical rules match, the processing always forms the longest match.

Historically, languages were designed using only characters from the ASCII character set even if they used Unicode encoding and allows Unicode values in strings and character literals. Adamant intentionally makes use of Unicode characters in some places when they improve readability. However, due to the difficulty of using Unicode characters on standard keyboards, these are always an alternative to more traditional ASCII notations.

Note: the current version of Adamant does not have a preprocessor. When one is added, it will be handled similarly to the C# preprocessor by including it in the lexical grammar.

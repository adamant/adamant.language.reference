## Tokens

Tokens form the terminals for the syntatic grammar and are comprised of [identifiers](identifiers.md), [keywords](keywords.md), [reserved words](reserved-words.md), [literals](literals.md), [operators, and punctuators](operators-and-punctuators.md). Whitespace and comments are not tokens, but may separate tokens.

```grammar
token
    : identifier
    | keyword
    | reserved_word
    | literal
    | operator_or_punctuator
    ;
```

## Grammars

This reference presents the syntax of Adamant using two grammars. The *lexical grammar* ([Lexical Grammar](lexical-grammar.md)) defines how characters combine to define tokens. The *syntactic grammar*([Syntactic grammar](syntactic-grammar.md)) defines how the tokens resulting from the lexical grammar are combined to form Adamant programs.

### Grammar Notation

The grammars in this reference use a variant of [BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form). They are not formal and may be ambiguous or have a structure which is not supported by certain parsing algorithms. Ambiguity in the grammar may be resolved by additional restrictions stated alongside the grammar. The notation used is summarized in the table below. Lexical grammars operator over unicode scalar values, thus whitespace cannot occur between nonterminals. When matching strings, the longest match is taken. Syntatic grammars operator over tokens and so nonterminals may be separated by whitespace.

| Syntax                                         | Meaning                                                                          |
| ---------------------------------------------- | -------------------------------------------------------------------------------- |
| *name*                                         | A nonterminal                                                                    |
| *name* `:` *expression* `;`                    | A derivation rule for the nonterminal *name*                                     |
| *expression* `\|` *expression*                 | Unordered choice                                                                 |
| *expression*`*`                                | Expression repeated zero or more times                                           |
| *expression*`+`                                | Expression repeated one or more times                                            |
| *expression*`?`                                | Expression is optional                                                           |
| *expression*`{`(*separator* `,`)? *n*`}`       | Repeat expression *n* times, optionally separated by *separator*                 |
| *expression*`{`(*separator* `,`)? *n*`,`*m*`}` | Repeat expression between *n* and *m* times, optionally separated by *separator* |
| *expression*`{`(*separator* `,`)? *n*`,``}`    | Repeat expression *n* or more times, optionally separated by *separator*         |
| `[`*character class*`]`                        | A regular expression style character class                                       |
| `(`*expression*`)`                             | Expression order of operations                                                   |
| `"`*literal*`"`                                | Matches a literal string                                                         |
| `<`*description*`>`                            | Matches a string according to the description                                    |
| `//` *comment* *eol*                           | A line comment                                                                   |
| `/*` *comment* `*/`                            | A multi-line comment                                                             |

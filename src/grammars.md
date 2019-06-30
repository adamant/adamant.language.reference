## Grammars

This reference presents the syntax of Adamant using two grammars. The *lexical grammar* defines how characters combine to define tokens. The *syntactic grammar* defines how the tokens resulting from the lexical grammar are combined to form Adamant programs.

### Grammar Notation

The grammars in this reference use a variant of [BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form). They are not formal and may be ambiguous or have a structure which is not supported by certain parsing algorithms. Ambiguity in the grammar may be resolved by additional restrictions stated alongside the grammar. The notation used is summarized in the table below. Lexical grammars operate over unicode scalar values, thus whitespace cannot occur between nonterminals. When matching strings, i.e. in lexical grammars, the longest match is taken. Syntactic grammars operate over tokens and nonterminals may be separated by whitespace.

| Syntax                                         | Meaning                                                                                                |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| *name*                                         | A nonterminal                                                                                          |
| *name* `:` *expression* `;`                    | A derivation rule for the nonterminal *name*                                                           |
| *expression* `\|` *expression*                 | Unordered choice                                                                                       |
| *expression*`*`                                | Expression repeated zero or more times                                                                 |
| *expression*`+`                                | Expression repeated one or more times                                                                  |
| *expression*`?`                                | Expression is optional                                                                                 |
| *expression*`{`(*separator* `,`)? *n*`}`       | Repeat expression *n* times, separated by *separator* if provided                                      |
| *expression*`{`(*separator* `,`)? *n*`,`*m*`}` | Repeat expression between *n* and *m* times, separated by *separator* if provided                      |
| *expression*`{`(*separator* `,`)? *n*`,*}`      | Repeat expression *n* or more times, separated by *separator* if provided                              |
| `[`*character class*`]`                        | A regular expression style character class (`^` negates a class, `\` escapes, and `-` matches a range) |
| `\u(`*hex digits*`)`                           | A single unicode code point                                                                            |
| `(`*expression*`)`                             | Expression order of operations                                                                         |
| `"`*literal*`"`                                | Matches a literal string (no escape sequences are supported)                                           |
| `?`*description*`?`                            | Matches a string according to the description                                                          |
| `//` *comment* *eol*                           | A line comment                                                                                         |
| `/*` *comment* `*/`                            | A multi-line comment                                                                                   |

# Lexical Structure

## Packages

The *package* is the fundamental unit of deployment in Adamant. A package may be an executable or library. A package is compiled from one or more *source files* and a *package configuration*. Source files are formally described as *compilation units*. A source file is a sequence of unicode scalar values encoded in UTF-8. It is a non-fatal compilation error for a source file to begin with a unicode byte order mark. Source files typically have a one-to-one correspondence with files in a file system, but this is not required. The location of the source files relative to a root directory for the package source files is the basis of namespaces within the package. The package configuration specifies configuration values needed to properly compile the package such as the root namespace, package name, referenced packages, and aliases for the referenced packages.

## Grammars

This references presents the syntax of Adamant using two grammars. The *lexical grammar* ([Lexical Grammar](#lexical-grammar)) defines how characters combine to define tokens. The *syntactic grammar*([Syntactic grammar](#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form Adamant programs.

### Grammar Notation

The grammars in this reference use a variant of [BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form). The grammars in this reference are not formal and may be ambiguous or have a structure which is not supported by certain parsing algorithms. Ambiguity in the grammar may be resolved by additional restrictions stated alongside the grammar. The notation used is summarized in the table below. When matching, the matching strings, the longest match is taken

| Syntax                                         | Meaning                                                                          |
| ---------------------------------------------- | -------------------------------------------------------------------------------- |
| *name*                                         | A nonterminal                                                                    |
| *name* `:` *expression* `;`                    | A derivation rule for the nonterminal *name*                                     |
| *expression* `|` *expression*                  | Unordered choice                                                                 |
| *expression*`*`                                | Expression repeated zero or more times                                           |
| *expression*`+`                                | Expression repeated one or more times                                            |
| *expression*`?`                                | Expression is optional                                                           |
| *expression*`{`(*separator* `,`)? *n*`}`       | Repeat expression *n* times, optionally separated by *separator*                 |
| *expression*`{`(*separator* `,`)? *n*`,`*m*`}` | Repeat expression between *n* and *m* times, optionally separated by *separator* |
| *expression*`{`(*separator* `,`)? *n*`,``}`    | Repeat expression *n* or more times, optionally separated by *separator*         |
| `(`*expression*`)`                             | Expression order of operations                                                   |
| `"`*literal*`"`                                | Matches a literal string                                                         |
| `'`*regex*`'`                                  | Matches strings matching the regular expression                                  |
| `<`*description*`>`                            | Matches a string according to the description                                    |
| `//` *comment* *eol*                           | A line comment                                                                   |
| `/*` *comment* `*/`                            | A multi-line comment                                                             |

## Lexical Grammar

```grammar
input
    : input_element*
    ;

input_element
    : whitespace
    | comment
    | token
    ;
```

For comments, see [Comments](comments.md).

### Whitespace

### Tokens

## Syntactic grammar

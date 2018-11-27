# Lexical Structure

## Packages

The *package* is the fundamental unit of deployment in Adamant. A package may be an executable or library. A package is compiled from one or more *source files* and a *package configuration*. Source files are formally described as *compilation units*. A source file is a sequence of unicode scalar values encoded in UTF-8. It is a non-fatal compilation error for a source file to begin with a unicode byte order mark. Source files typically have a one-to-one correspondence with files in a file system, but this is not required. The location of the source files relative to a root directory for the package source files is the basis of namespaces within the package. The package configuration specifies configuration values needed to properly compile the package such as the root namespace, package name, referenced packages, and aliases for the referenced packages.

## Grammars

This references presents the syntax of Adamant using two grammars. The *lexical grammar* ([Lexical Grammar](#lexical-grammar)) defines how characters combine to define tokens. The *syntactic grammar*([Syntactic grammar](#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form Adamant programs.

### Grammar Notation

The grammars in this reference use a variant of [BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form). They are not formal and may be ambiguous or have a structure which is not supported by certain parsing algorithms. Ambiguity in the grammar may be resolved by additional restrictions stated alongside the grammar. The notation used is summarized in the table below. Lexical grammars operator over unicode scalar values, thus whitespace cannot occur between nonterminals. When matching strings, the longest match is taken. Syntatic grammars operator over tokens and so nonterminals may be separated by whitespace.

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
| `[`*character class*`]`                        | A regular expression style character class                                       |
| `(`*expression*`)`                             | Expression order of operations                                                   |
| `"`*literal*`"`                                | Matches a literal string                                                         |
| `<`*description*`>`                            | Matches a string according to the description                                    |
| `//` *comment* *eol*                           | A line comment                                                                   |
| `/*` *comment* `*/`                            | A multi-line comment                                                             |

## Lexical Grammar

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

### Line Terminators

```grammar
new_line
    : <Carriage return character (U+000D)>
    | <Line feed character (U+000A)>
    | <Carriage return character (U+000D) followed by line feed character (U+000A)>
    | <Next line character (U+0085)>
    | <Line separator character (U+2028)>
    | <Paragraph separator character (U+2029)>
    ;
```

### Whitespace

```grammar
whitespace
    : <Any character with Unicode class Zs>
    | <Horizontal tab character (U+0009)>
    | <Vertical tab character (U+000B)>
    | <Form feed character (U+000C)>
    ;
```

### Tokens

```grammar
token
    : identifier
    | keyword
    | reserved_word
    | literal
    | operator_or_punctuator
    ;
```

### Keywords

**TODO:** exclude from this list tokens that fall in other categories? (like literals)

```grammar
keyword
    :
    | "abstract"
    | "and"
    | "as"
    | "base"
    | "bool"
    | "break"
    | "byte"
    | "class"
    | "const"
    | "copy"
    | "delete"
    | "else"
    | "ensures"
    | "enum"
    | "explicit"
    | "false"
    | "float"
    | "float32"
    | "fn"
    | "foreach"
    | "get"
    | "if"
    | "implicit"
    | "in"
    | "init"
    | "int"
    | "int8"
    | "int16"
    | "int64"
    | "invariant"
    | "let"
    | "loop"
    | "match"
    | "may"
    | "metatype"
    | "move"
    | "mut"
    | "namespace"
    | "new"
    | "never"
    | "next"
    | "no"
    | "none"
    | "not"
    | "offset"
    | "operator"
    | "or"
    | "override"
    | "owned"
    | "params"
    | "protected"
    | "public"
    | "published"
    | "ref"
    | "return"
    | "requires"
    | "safe"
    | "self"
    | "Self"
    | "set"
    | "size"
    | "struct"
    | "throw"
    | "true"
    | "tuple"
    | "type"
    | "uint"
    | "uint16"
    | "uint64"
    | "uninitialized"
    | "unsafe"
    | "using"
    | "var"
    | "void"
    | "where"
    | "while"
    ;
```

## Syntactic grammar

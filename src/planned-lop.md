## Language-Oriented Programming

[Language-oriented programming](https://en.wikipedia.org/wiki/Language-oriented_programming) (LOP) is a programming paradigm that focuses on constructing software out of not only software modules but out of languages. Thus developers create multiple domain specific languages (DSLs) for solving problems and then use those languages to solve them. Adamant's LOP is largely inspired by the Racket language and in particular the [brag library](https://docs.racket-lang.org/brag/) as used in the online book [Beau­tiful Racket](https://beautifulracket.com/) by Matthew Butt­erick.

### Code Expressions

Code expressions are chunks of DSL code embedded in expressions for computing some or all of the expression's value. Code expressions are similar to code spans in Markdown. They start and end with a backtick. If the DSL code contains backticks, this can be handled by surrounding the code expression in multiple backticks. Any number of backticks is acceptable, but the start and end of the code expression must match. A trimming scheme allows code expressions that begin or end with backticks. Any code expression which begins (or ends) with whitespace followed by a backtick character will have the first space character trimmed from the end of the DSL code. (Note this is similar too but slightly different than the rule used in Markdown.) Type inference is used to determine the correct language to use for a code expression. Each language registers the type(s) it can create expressions for. The compiler selects the language that can fill the necessary type in the containing expression and is in scope. Languages are imported through namespaces the same as a class might be. There may be additional options for language selection. For example, languages may be able to provide a regular expression that must match the beginning or end of a code expression in order for that language to be considered. One possible use case for code expressions is embedding SQL statements in code safely. Another use case is regular expressions.

```adamant
var query = `select * from User where PasswordHash=HashedPassword`;
var results = db.run(query);
```

```adamant
var matches = str.match(`(a|b)+\|[^ ]*`);
```

### Code Blocks

Code blocks allow DSL code to be placed at the statement or declaration level. A code block is begun by a line that begins with whitespace followed by three or more consecutive backticks. The backticks are followed by the language parameters which determine which language is used in the code block and any options needed for compiling the language. The info string may not contain any backtick characters (this ensures a code block is distinct from a code expression). The code block is ended by a line beginning with the same whitespace as the block start followed by teh same number of backticks as the start and a newline. The block end may optionally contain whitespace before the line end. A code block may also be terminated by the end of the file if it was not nested inside any other declarations. This allows a whole code file to easily be in another language by having the first line be "` ``` language `". One use case for code blocks would be a parser generator based on a BNF like notation.

````adamant
``` parser.BNF
exp = a "+" b
    | a "-" b
    | a "*" b
    | a "/" b
    ;
````

Code blocks may also allow for setting a default language so the language can be omitted with repeated blocks in a file. One way to do this would be with a `##lang` preprocessor directive. Alternatively, `##lang` could be an alternate way to specify a whole file as being in another language. That way code blocks could be required to be closed.

### Code Files

It should be possible to register file extensions with the Adamant compilation process in a way that allows files with those extensions to be picked up and compiled as the matching language.

### Code Compilation

The details of code expression and block compilation have not been worked out. It is expected that something similar to what Racket uses can be made to work. However, this may require a standard model of the Adamant AST. Code compilations should be built on top of macro and compile time function execution functionality. As with Racket, there should probably be ways to easily create languages with macro like facilities and ways to take full control of lexing, parsing, and translation. A default lexer should be provided that is capable of lexing a superset of Adamant. For example, in additional to supporting all Adamant tokens it may contain rules for tokenizing other symbols into operator tokens.

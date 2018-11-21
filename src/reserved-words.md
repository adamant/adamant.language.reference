## Reserved Words

The following words are reserved for future use as Adamant keywords. To use these as an identifier, they must be escaped (i.e. "`\continue`").

### Reserved Keywords

| Word               | Possible Uses                                                              |
| ------------------ | -------------------------------------------------------------------------- |
| `alias`            | Planned Type Alias Feature                                                 |
| `case`             | Useful for switch like constructs                                          |
| `cast`             | Casting                                                                    |
| `checked`          | Checked Operations                                                         |
| `const_cast`       | Casting                                                                    |
| `continue`         | Useful for control flow                                                    |
| `default`          | Useful for switch like constructs and default values                       |
| `defer`            | Swift style "`defer`" statements                                           |
| `do`               | "`do while`" loop or Swift style "`do`" block                              |
| `dynamic_cast`     | Casting                                                                    |
| `extend`           | Extensions                                                                 |
| `extension`        | Extensions                                                                 |
| `external`         | Common Keyword                                                             |
| `fallthrough`      | Useful for switch like constructs                                          |
| `for`              | Common Keyword                                                             |
| `from`             | Common Keyword                                                             |
| `guard`            | Swift style guard statements                                               |
| `internal`         | Common Keyword                                                             |
| `null`             | Null value for pointers                                                    |
| `otherwise`        | Loop Else                                                                  |
| `partial`          | Partial Classes                                                            |
| `private`          | Common Keyword                                                             |
| `reinterpret_cast` | Casting                                                                    |
| `repeat`           | Swift style "`repeat {} while condition;`" loop                            |
| `replace`          | Partial Classes                                                            |
| `select`           | C# style query                                                             |
| `sizeof` `size_of` | Size of Operator                                                           |
| `switch`           | Useful for switch like constructs                                          |
| `symmetric`        | Symmetric operators                                                        |
| `transmute`        | Reinterpret Cast                                                           |
| `then`             | Python style loop else<sup>1</sup>                                         |
| `unchecked`        | Unchecked Operations                                                       |
| `unless`           | Ruby style `if not` statement or `unless break` for Python style loop else |
| `when`             | C# style exception filters                                                 |
| `xor`              | Logical exclusive or operator                                              |
| `yield`            | Generators                                                                 |

<sup>1</sup> The "`then`" keyword would allow for "`while condition { } then { } else { }`". The "else" block would be executed if the condition is false the first time it is evaluated. The "then" block would be executed in control flow left the end of the loop body (i.e. the loop body executed at least once and wasn't exited with a "`break`" expression). Note though that a "`then`" without an "`else`" would execute even if the loop never did. (see also `unless break`)

### Reserved Simple Types

The following identifier patterns are reserved for use as simple types:

| Pattern           |                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------- |
| `int`*n*          | Where *n* is any sequence of digits not already defined to be an integral type.       |
| `uint`*n*         | Where *n* is any sequence of digits not already defined to be an integral type.       |
| `float`*n*        | Where *n* is any sequence of digits not already defined to be an floating point type. |
| `fixed`           |                                                                                       |
| `fixed`*n*`.`*m*  | Where *n* and *m* are any sequence of digits.                                         |
| `ufixed`          |                                                                                       |
| `ufixed`*n*`.`*m* | Where *n* and *m* are any sequence of digits.                                         |
| `decimal`         |                                                                                       |
| `decimal`*n*      | Where *n* is any sequence of digits.                                                  |
| `real`            |                                                                                       |
| `real.`*n*        | Where *n* is any sequence of digits.                                                  |

Note that patterns with leading zeros are also reserved.

### Reserved Word Errors

Using a reserved word other than "`continue`" as an identifer is a non-fatal compiler error. The reserved word "`continue`" is treated as a synonym for "`next`" that produces a non-fatal compiler error.

### Symbols

Symbols not already defined are not technically reserved. They are simply invalid character sequences in Adamant. However, the following symbols are "reserved" for the below uses:

| Symbol  | Use                           |
| ------- | ----------------------------- |
| `**`    | Raise to power.               |
| `` ` `` | Reserved for use in Markdown. |
| `##`    | Preprocessor                  |

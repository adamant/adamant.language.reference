## Comments

Both single line and delimited comments are supported. Single-line comments start with the characters "`//`" and extend to the end of the source line. Delimited comments start with the characters "`/*`" and end with the characters "`*/`". Delimited comments may span multiple lines. Comments starting with "`///`" are used for documentation (see [Documentation Comments](documentation-comments.md))

```grammar
comment
    : single_line_comment
    | delimited_comment
    ;

single_line_comment
    : "//" <any unicode character exception newline>*
    ;

delimited_comment
    : "/*" <any unicode character sequence not containing asterisk slash> "*/
    ;
```

Comments do not nest. The character sequences "`/*`" and "`*/`" have no special meaning within a single-line comment, and the character sequences "`//`" and "`/*`" have no special meaning within a delimited comment.

Comments are not processed within string literals or user defined literals.

```adamant
// A single line comment

/* this is a block comment
let x = 5; // it can contain code and other comments
*/
```

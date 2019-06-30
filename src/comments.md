## Comments

Both single line and delimited comments are supported. Single-line comments start with the characters "`//`" and extend to the end of the source line. Delimited comments start with the characters "`/*`" and end with the characters "`*/`". Delimited comments may span multiple lines. Comments starting with "`///`" are used for documentation (see [Documentation Comments](documentation-comments.md))

```grammar
comment
    : single_line_comment
    | delimited_comment
    ;

single_line_comment
    : "//" non_newline_character*
    ;

input_character // every character not included in the newline rule
    : [^\u(000D)\u(000A)\u(0085)\u(2028)\u(2029)]
    ;

delimited_comment
    : "/*" delimited_comment_section* "*"+ "/"
    ;

delimited_comment_section
    : "/"
    | "*"* [^/*]+ // i.e. any number of asterisks followed by one or more characters that aren't slash or asterisk
    ;
```

Comments do not nest. The character sequences "`/*`" and "`*/`" have no special meaning within a single-line comment, and the character sequences "`//`" and "`/*`" have no special meaning within a delimited comment.

Comments are not processed within string literals or user defined literals.

An example including both single line and delimited comments is:

```adamant
// A single line comment

/* this is a block comment
let x = 5; // it can contain code and other comments
*/
```

# Expressions

Expressions can be expression blocks which are blocks or result expressions, however a number of places in the grammar do not allow expression blocks. For that purpose, an embedded expression is any expression that is not an expression block.

```grammar
expression
    : expression_block
    | embedded_expression
    ;

embedded_expression
    : choice_expression
    | loop_expression
    ;
```


# Expressions

Expressions can be expression blocks which are blocks or result expressions, however a number of places in the grammar do not allow expression blocks. For that purpose, and embedded expression is any expression that is not an expression block.

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

## Boolean Expression

A boolean expression is an embedded expression whose type is "`bool`".

```adamant
boolean_expression
    : embedded_expression
    ;
```

## Order of Operations

Expressions are evaluated left to right. Arguments to function calls are evaluated left to right. If an expression is a constant expression or contains only pure functions/operations, then the compiler may execute them in any order for optimization purposes.

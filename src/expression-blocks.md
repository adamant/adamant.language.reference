## Expression Blocks

```grammar
expression_block
    : block
    | result_expression
    ;
```

### Blocks

A block is zero or more statements enclosed in curly braces. If the block contains result expressions, then the type of the expression is the type that all the result expression types are compatible with. If a block contains no result expressions, then the type of the expression is "`void`".

```grammar
block
    : "{" statement* "}"
    ;
```

### Result Expressions

A result expression is used to provide the value a block evaluates to. Certian control flow expressions like "`if`" allow a result expression to be used directly as the body of the expression. In that case the block has the type of the expression and yields the expression's value. When a result expression is used inside a block in other contexts, then all control flow paths in the block must lead to a result expression. The type of each result expression is "`never`". The type of the block is determined by taking the types of the expressions of all result expressions in the block and finding a common type they can all be converted to. It is an error to have statements or expressions after the control flow is terminated by the result expression.

```grammar
result_expression
    : "=>" expression
    ;
```

A result expression can't be used outside of a block. However, it can be used to create a block that can be used as expression with a value.

```adamant
var x = => "Hello"; // ERROR
x = {
        DoSomething();
        => "Goodbye";
    };
```

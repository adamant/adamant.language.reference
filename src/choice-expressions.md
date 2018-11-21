## Choice Expressions

There are two constructs which allow the choice of which code to execute. They are "`if`" and "`match`".

```grammar
choice_expression
    : if_expression
    | match_expression
    ;
```

## `if` Expression

An if expression evaluates a condition. If that condition is true, the first expression block is executed. If that condition evaluates to false, then the else clause is evaluated if it is present. The type of an if expression with no else clause and expression block type "`T`" is "`T?`" unless "`T`" is "`void`" in which case the if expression has type "`void`". When the condition is true, the value of the expression is the value of the expression block. When the condition is false, the value is "`none`". The type of an if expression with an else clause is the type both the expression block type and else clause type can be assigned to. If the condition is true, the value of the if expression is the value of the expression block, otherwise it is the value of the else clause.

```grammar
if_expression
    : "if" boolean_expression expression_block else_clause
    ;

else_clause
    : "else" expression_block
    | "else" if_expression
    | // epsilon
    ;
```

The else clause is associated with the lexically closest if expression. Thus "`if x => if y => F() else => G()`" is equvalent to:

```adamant
if x
{
    => if y
    {
        => F();
    }
    else
    {
        => G();
    };
}
```

Examples:

```adamant
if condition
{
    // executed if condition is true
}

if condition
{
    // executed if condition is true
}
else
{
    // executed if condition is false
}

if condition1
{
    // executed if condition1 is true
}
else if condition2
{
    // executed if condition1 is false and condition2 is true
}
else // optional else
{
    // executed if condition1 is false and condition2 is false
}

let x = if condition => "true" else => "false";
let y = (if condition => "value") ?? "default";

let x = if condition
    {
        DoSomething();
        => "true";
    }
    else
    {
        DoSomethingElse();
        => "false";
    }; // semi-colon ends let statement


```

## `match` Expressions

A match expression allows the selection of choices based on matching a value to patterns.

```adamant
match v
{
    [0, y]
    {
        Action(y);
    },
    _ // Matches anything
    {
        SomethingElse();
    }, // last comma optional
}
```

The compiler checks that the conditions are exhaustive.

Like "`if`", "`match`" is an expression of type "`void`" by default, but can be given an expression value using expression blocks.

```adamant
let z = match v
    {
        [0, y]
        {
            Action();
            => y;
        },
        [x, 0] => x,
        [x, y] => x + y, // last comma optional
    };
```

 The type of the match expression is the type all the match arms are compatible with.

```grammar
match_expression
    : "match" embedded_expression "{" match_arm{",", 1, } ","? "}"
    ;

match_arm
    : pattern expression_block
    ;
```

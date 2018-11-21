## Loop Expressions

Adamant has three loop constructs. They are "`loop`", "`while`" and "`foreach`". All loop bodies must be block statements.

```grammar
loop_expression
    : simple_loop_expression
    | while_expression
    | foreach_expression
    ;
```

### `loop`

The simplest kind of loop is an infinite loop. Infinite loops are created with the "`loop`" keyword.

```grammar
simple_loop_expression
    : loop_label? "loop" block
    ;
```

Example:

```adamant
loop
{
    console.write_line("Loop Forever");
}
```

### `while`

```grammar
while_expression
    : loop_label? "while" boolean_expression block
    ;
```

Example:

```adamant
while condition
{
    // do work
}
```

You might be tempted to write an infinite loop as "`while true`" however "`loop`" is better for this because the compiler treats them differently for definite assignment. Namely, a variable can be definitely assigned in a "`loop`" but will not be considered definitely assigned by a "`while true`" loop.

If you need something like a "`do {} while`" loop available in many other languages, the proper way to do that is:

```adamant
loop
{
    // do work
    if not condition => break;
}
```

### `foreach`

Foreach loops allow something to be done a set number of times or for a set of values.

```grammar
foreach_expression
    : loop_label "foreach" "var"? pattern "in" embedded_expression block
    ;
```

Example:

```adamant
foreach x in 1..10
{
    console.write_line("{x}");
}
```

The `foreach` loop can operate over any value that is iterable.

Sometimes the actual value of the loop variable isn't needed. This can be denoted using the underscore.

```adamant
foreach _ in 1..10
{
    console.write_line("Hi");
}
```

**TODO:** show how to handle references to structs

### Controlling Loop Iteration

You can use `break` and `next` expressions to control loop iteration. Both "`break`" and "`next`" expressions have the type "`never`".

```grammar
break_expression
    : "break" loop_label? expression?
    ;

next_expression
    : "next" loop_label?
    ;
```

### `break` Values

The "`break`" expression can have a value. This value then becomes the value of the loop expression. All break expressions must have values of compatible types. The type of a "`loop`" expression is the type of the values.

Example:

```adamant
let x = foreach x in items
        {
            do_something();
            if condition
            {
                break get_value();
            }
        } ?? alternate_value;
```

The  "`while`" and "`foreach`" loop return an optional value to indicate the loop body never executed. In that situations, the value of the loop expression is "`none`". This allows you to use the coalesce operator to easily specify a value for that case.

```adamant
let x = foreach y in items
        {
            if condition
            {
                break 234;
            }
        } ?? foo;
```

### Loop Labels

When using break or next it may be necessary to indicate which loop you are controlling. This can be done by labeling the loop. Loop labels are essentially lifetimes and are named as such.

```grammar
loop_label
    : "$" identifer ":"
    ;
```

Example:

```adamant
$outer: foreach x in 0..10
{
    $inner: foreach y in 0..10
    {
        if x % 2 == 0 => next $outer;
        if y % 2 == 0 => next $inner;
        console.write_line("x = {x}, y = {y}");
    }
}
```

When loop labels are combined with break values, the label is placed before the value as `break $label value;`.

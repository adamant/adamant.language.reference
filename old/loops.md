# Loops

Adamant has three loop constructs. They are `loop`, `while` and `foreach`. All loop bodies must be block statements.

## `loop`

The simplest kind of loop is an infinite loop. Infinite loops are created with the `loop` keyword. They look like this.

```adamant
loop
{
    console.write_line("Loop Forever");
}
```

## `while`

While loops look like:

```adamant
while condition
{
    // do work
}
```

You might be tempted to write an infinite loop as `while true` however `loop` is better for this because the compiler treats them differently for definite assignment. Namely, a variable can be definitely assigned in a `loop` but will not be considered definitely assigned by a `while true` loop.

If you need something like a `do {} while` loop available in many other languages, the proper way to do that is:

```adamant
loop
{
    // do work
    if not condition => break;
}
```

## `foreach`

For loops allow something to be done a set number of times or for a set of values.

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

## Enumerate

The standard library provides an `enumerate()` method for counting. This provides the index of each value in the iterator sequence.

```adamant
foreach #(i, x) in (5..10).enumerate()
{
    console.write_line("i = \(i), x = \(x)");
}
```

Outputs:

```console
i = 0, x = 5
i = 1, x = 6
i = 2, x = 7
i = 3, x = 8
i = 4, x = 9
i = 5, x = 10
```

## Controlling Loop Iteration

You can use `break` and `next` statements like in many other languages such as C# and Java.

### `break` values

The `break` statement can have a value. This value then becomes the value of the loop expression. All break statements must have values of compatible types. The type of a `loop` expression is the type of the values. Both `break` and `next` can be used as expressions whose type are `never`.

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

The  `while` and `foreach` loop return an optional value to indicate that the loop exited because the condition evaluated to false. This may mean the loop was never executed. In those situations, the value of the loop expression is `none`. This allows you to use the coalesce operator to easily specify a value for that case.

```adamant
let x = for let y in sdf
        {
            if cond
            {
                break 234;
            }
        } ?? foo;
```

## Loop Labels

When breaking or continuing it may be necessary to indicate which loop you are controlling. This can be done by labeling the loops. Loop labels are essentially lifetimes and are named as such.

```adamant
$outer: foreach x in 0..10
{
    $inner: foreach y in 0..10
    {
        if x % 2 == 0 => continue $outer;
        if y % 2 == 0 => continue $inner;
        console.write_line("x = {x}, y = {y}");
    }
}
```

When loop labels are combined with break values, the label is placed before the value as `break $label value;`.

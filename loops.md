# Loops

Adamant has three loop constructs. They are `loop`, `while` and `for`. All loops bodies must be block statements.

## `loop`

The simplest kind of loop is an infinite loop. Infinite loops are created with the `loop` keyword. They look like this.

    loop
    {
        console.WriteLine("Loop Forever");
    }

## `while`

While loops look like:

    while condition
    {
        // do work
    }

You might be tempted to write an infinite loop as `while true` however `loop` is better for this because the compiler treats them differently for definite assignment. Namely, a variable can be definitely assigned in a `loop` but will not be considered definitely assigned by a `while true` loop.

If you need something like a `do {} while` loop available in many other languages, the proper way to do that is:

    loop
    {
        // do work
        if not condition { break; }
    }

## `for`

For loops allow something to be done a set number of times or for a set of values.

    for let x in 0..10
    {
        console.WriteLine("{x}");
    }

The `for` loop can operate over any value that is iterable.

Sometimes the actual value of the loop variable isn't needed. This can be denoted using the underscore.

    for _ in 0..10
    {
        console.WriteLine("Hi");
    }

**TODO:** maybe for loops should be like arguments and not require the `let` keyword, but require the `var` keyword?
**TODO:** show how to handle references to structs

## Enumerate

Standard library provides `Enumerate()` method for counting. This provides the index of each value in the iterator sequence.

    for let [i, x] in (5..10).Enumerate()
    {
        console.WriteLine("i = {i}, x = {x}");
    }

Outputs:

    i = 0, x = 5
    i = 1, x = 6
    i = 2, x = 7
    i = 3, x = 8
    i = 4, x = 9

## Controlling Loop Iteration

You can use `break` and `continue` statements like in many other languages such as C# and Java.

### `break` values

The `break` statement can have a value. This value then becomes the value of the loop expression. All break statements must have values of compatible types. The type of a `loop` expression is the type of the values. The type of `while` and `for` loops are optional because the value is `none` if the loop never executes or exits without breaking.

    let x = loop
            {
                DoSomething();
                if condition
                {
                    break GetValue();
                }
            };

## Loop Labels

When breaking or continuing it may be necessary to indicate which loop you are controlling. This can be done by labeling the loops. Loop labels are essentially lifetimes and are named as such.

    ~outer: for let x in 0..10
    {
        ~inner: for let y in 0..10
        {
            if x % 2 == 0 { continue ~outer; }
            if y % 2 == 0 { continue ~inner; }
            console.WriteLine("x = {x}, y = {y}");
        }
    }

When loop labels are combined with break values, the label is placed before the value as `break ~label value;`.

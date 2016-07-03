# Loops

**Idea: switch to Rust style where there are no parens and curly braces are required, except allow another control flow statement instead of a block.  So for example one could nest multiple loops with only the inner one needing braces.**

Adamant has three loop constructs.  They are `loop`, `while` and `for`.

## `loop`

The simplest kind of loop is an infinite loop.  Infinite loops are created with the `loop` keyword.  They look like this.

	loop
	{
		console.WriteLine("Loop Forever!");
	}

Note that the statement doesn't have to be a block.

## `while`

While loops look like:

	while(condition)
	{
		// do work
	}

You might be tempted to write an infinite loop as `while(true)` however `loop` is better for this because the compiler treats them different for definite assignment.  Namely, a variable can be definitely assigned in a `loop` but will not be considered definitely assigned by a `while(true)` loop.

If you need the something like a `do ... while()` loop available in many other languages, the proper way to do that is:

	loop
	{
		// do work
		if(!condition) break;
	}

## `for`

For loops are Rust style like:

	for(let x in 0..10)
		console.WriteLine("{x}");

The for loop can operate over any value that is an iterator.

## Enumerate

Standard library provides `Enumerate()` method for counting

	for(let [i, x] in (5..10).Enumerate())
		console.WriteLine("i = {i}, j = {j}");

Outputs:

	i = 0, j = 5
	i = 1, j = 6
	i = 2, j = 7
	i = 3, j = 8
	i = 4, j = 9

## Controlling Loop Iteration

You can use `break` and `continue` statements like in many other languages such as C# and Java.

## Loop Labels

When breaking or continuing it may be necessary to indicate which loop you are controlling.  This can be done by labeling the loops.  Loop labels are essentially lifetimes and are named as such.

	~outer: for(let x in 0..10)
	{
		~inner: for(let y in 0..10)
		{
			if(x % 2 == 0) continue ~outer;
			if(y % 2 == 0) continue ~inner;
			console.WriteLine("x = {x}, y = {y}");
		}
	}
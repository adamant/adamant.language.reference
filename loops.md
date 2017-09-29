# Loops

Adamant has three loop constructs.  They are `loop`, `while` and `for`.  As in Rust, all loops bodies must be block statements.

## `loop`

The simplest kind of loop is an infinite loop.  Infinite loops are created with the `loop` keyword.  They look like this.

	loop
	{
		console.WriteLine("Loop Forever!");
	}


## `while`

While loops look like:

	while condition
	{
		// do work
	}

You might be tempted to write an infinite loop as `while true` however `loop` is better for this because the compiler treats them different for definite assignment.  Namely, a variable can be definitely assigned in a `loop` but will not be considered definitely assigned by a `while true` loop.

If you need something like a `do ... while` loop available in many other languages, the proper way to do that is:

	loop
	{
		// do work
		if !condition { break; }
	}

## `for`

For loops are Rust style like:

	for let x in 0..10
	{
		console.WriteLine("{x}");
	}

The for loop can operate over any value that is an iterator.

## Enumerate

Standard library provides `Enumerate()` method for counting

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

## Loop Labels

When breaking or continuing it may be necessary to indicate which loop you are controlling.  This can be done by labeling the loops.  Loop labels are essentially lifetimes and are named as such.

	~outer: for let x in 0..10
	{
		~inner: for let y in 0..10
		{
			if x % 2 == 0 { continue ~outer; }
			if y % 2 == 0 { continue ~inner; }
			console.WriteLine("x = {x}, y = {y}");
		}
	}
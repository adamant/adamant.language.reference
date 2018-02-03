# Choice Expressions

There are two constructs which allow the choice of which code to execute.  They are `if` `else` and `match`.

## `if` Statement

	if condition
	{
		// executed if condition is true
	}

### `else` Statement

	if condition
	{
		// executed if condition is true
	}
	else
	{
		// executed if condition is false
	}

### `else if` Statement

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

### `if` Expressions

If is actually an expression that can evaluate to a value.  By default the value of an `if` expression is `void`.  Just like a function call returning `void`, they can't be used inside an expression because there is no value of the type `void`.  However, an expression block can be used to provide a value for a branch of the `if`.

	let x = if condition => "true" else => "false";


This can be used with `else if` as well.  Each `if` must have an expression block and the values must be convertible to a common type.

For `if` expressions without an else case, the expression evaluates to `null` in the else case.  So if the result of the then block is of type `T`, then the result of the whole expression is of type `T?`.

	let x = (if condition => "value") ?? "default";

When more complex operations are needed in an if expression, expression blocks with braces can be used.

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

It is an error to have statements after the `=>` statement in the block.

Note that expression blocks can only be used where a block expression is expected, so neither of these statements will compile.

	var x = => "Hello";
	x = {
			DoSomething();
			=> "Goodbye";
		};

## `match` Statement

A match statement allows the selection of choices based on matching a value to patterns.

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

The compiler checks that the conditions are exhaustive.

## `match` Expressions

Like `if`, `match` is an expression of type `void` by default, but can be given an expression value using expression blocks.

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

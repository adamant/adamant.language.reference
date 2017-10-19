# Choice Expressions

There are two constructs which allow the choice of which code to execute.  They are `if` `else` and `match`.

## `if`

	if condition
	{
		// executed if condition is true
	}

### `else`

	if condition
	{
		// executed if condition is true
	}
	else
	{
		// executed if condition is false
	}

### 'else if`

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

### `if` Expressions and `is` Values

If is actually an expression that can evaluate to a value.  By default the value of an `if` expression is `void`.  Just like a function call returning `void`, they can't be used inside an expression because there is no value of the type `void`.  However, an `is` statement can be used to provide a value for a branch of the `if`.

	let x = if condition
		{
			// executed if condition1 is true
			is "true";
		}
		else
		{
			// executed if condition is false
			is "false";
		}; // semi-colon ends let statement

This can be used with `else if` as well.  All branches of the if must end in an is statement and the values must be convertible to a common type.  It is an error to have statements after the `is` statement in the block.

For `if` expressions without an else case, the result type is nullable.  So if the result of the then block is of type `T`, then the result of the whole expression is of type `T?` and the null value is returned if no choice is selected.

	let x = if condition { is "value"; } ?? "default";

### Block Expressions

You can actually just use a block as an expression.

	let x = {
				// some statements
				is "result";
			}; // semi-colon ends let statement

### Block Level `is`

The `is` statement can't be used outside of a block, but is can be used where a block would otherwise be expected in an `if` or `match` expression.  This leads to a very nice short syntax for conditional expressions.

	let x = if condition is "true" else is "false";

## `match`

A match expression allows the select of choices based on matching a value to patterns.  Like `if`, it is an expression of type `void` if no value is provided.

	let z = match v
			{
				[0, y]
				{
					Action();
					is y;
				},
				[x, 0] is x,
				[x, y] is x + y, // last comma optional
			};

The compiler checks that the conditions are exhaustive.
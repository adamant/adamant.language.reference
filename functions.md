# Functions

Every Adamant program has at least one function, the main function:

	public Main() -> void
	{
	}

This is the simplest possible function.  It is public, meaning it is visible everywhere including outside of this package (see [Access Modifiers](access-modifiers.md). That is followed by the name of the function.  The empty parentheses indicate the function takes no arguments. The `->` indicates this is a function declaration and is followed by the return type.  This function doesn't return a value which is indicated with the special type `void`.

So how to take arguments?  Here is a main function that takes the console as an argument, so it can print hello world.

	public Main(console: mut Console) -> void
	{
		console.WriteLine("Hello World!");
	}

Function parameters are declared like `let` bindings.  In fact, they are completely equivalent to them except the type must always be fully annotated.  No type inference is done on function parameters.  Multiple arguments are separated by commas.  In the rare circumstance where a parameter needs to be a mutable binding, preceded it with the var keyword.

	public WriteIt(console: mut Console, var x: int) -> void
	{
		console.WriteLine("x = {x}");
		x -= 1; // we can assign a new value to `x` because we declared it with var
		console.WriteLine("x - 1 = {x}");
	}

## Returning from a Function

To return a value simply declare the correct return type and use a return statement.

	public AddTwo(x: int) -> int
	{
		return x + 2;
	}

To return from a function whose return type is `void`, use a return statement without a value, as `return;`.

## Diverging Functions

Adamant has special syntax for [diverging functions](https://en.wikipedia.org/wiki/Divergence_(computer_science)).  Those are functions that never return by normal means.  That could be because they always cause a panic (terminate the program), throw an exception or loop forever.

	public Diverges() -> !
	{
		Panic("Stop the program now!");
	}

The function panic is a special function that crashes the program immediately, outputting the message passed to it.  The `Diverges` function never returns, because it crashes the program.  This is indicated with the special return type `!`.

A diverging function can be used where an expression of any type is expected.  Thus the type `!` is a subtype of all other types.  In formal type theory, it is the [bottom type](https://en.wikipedia.org/wiki/Bottom_type).  This can be useful in situations where two expressions are expected to have the same type, but you want one to be an error.  For example, using an [`if` expression](choice.md#if_expression), we might write:

	let y: string = if condition => "We're good" else => Panic();

## Function References

We can also create variable bindings that reference a function.

	let f: (int) -> int;

`f` is a variable that references a function taking an `int` and returning an `int`.  It can be assigned a function and called as a function.  For example:

	// Without type inference
	let f: (int) -> int = AddTwo;

	// With type inference
	let f = AddTwo;  // f: (int) -> int

	// Calling f
	let five = f(2);
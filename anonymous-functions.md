# Anonymous Functions

Anonymous functions allow the creation of unnamed functions inside another function.

	let addTwo = (x) -> x + 2; // addTwo: (int) -> int

The parameter types can be inferred.  However, sometimes it is necessary or clearer to directly state them.

	let sayHello = (name: string) -> "Hello " + name + "!";

The above examples are simple functions that only require a single expression to compute.  However, an anonymous function can have a complete function body by using a block.

	let repeatN = (n, func) ->
		{
			for _ in 0..n
			{
				func();
			}
		};

**TODO:** should I support dropping the parentheses as C# does?  If so, should it be required when it is possible to in order to avoid fights over the preferred style? (or issue a warning?)

**TODO:** Rust allows the return type to be stated.  Is that needed?

## Closures

Anonymous functions are closures, thus they have access to the variable values in the surrounding function.

	MakeAdder(n: int) -> (int) -> int
	{
		return x -> x + n;
	}

**TODO:** explain the complexities of closures in a language with a borrow checker
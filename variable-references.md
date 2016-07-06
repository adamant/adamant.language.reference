# Variable References

So far, we have seen reference types and value types

** Refs may be more complicated than they are worth. Notice, C# only has ref and out params and that only because of difficulties with multiple returns **

While all variables hold their values by reference, the `ref` keyword allows you to create and use references to `var` variable bindings.  To avoid ambiguity, variables containing variable references must themselves be immutable (i.e. you have to use `let` not `var`). A variable reference lets you update the binding of another variable.

	var x = 5;
	{
		let y = ref x;
		y = 6;
	}
	console.WriteLine("x = {x}"); // prints: 6

By default variable reference borrows an immutable reference to the value.  Use `mut` to borrow a mutable reference.

	var c: mut = new Customer("Dorthy");
	{
		let d = ref mut c;
		d.Age = 32;
	}
	console.WriteLine("{c.Name} is {c.Age} years old"); // prints: Dorthy is 32 years old

Because taking a reference to a variable also borrows a reference, all borrow rules apply.  However, additionally, the referenced variable is inaccessible as long as the reference exists.  Passing a reference, moves the reference.

To be clear, you can't take a reference to a `let` variable binding becuase the variable binding is immutable.

	let x = 5;
	let y = ref x; // complier error: can't take reference to let binding

Reference arguments of functions, borrow their references.

	var x = 5;
	let y = ref x;
	FunctionTakingReference(y); // borrows y, allows function to update x

TODO: what if you take an immutable reference and assign it a value, but now the original person can mutate and object you couldn't?  There needs to be some additional rules here.

TODO: refs can mess with ownership of the variable they modify
	
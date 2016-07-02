# Ownership & Borrowing

**For clarity, perhaps the word reference should be reserved for the `ref` keyword

By default all variable bindings are immutable.

	let x = 5;
	x = 6; // compilation error
	
To declare a mutable variable binding, use the `var` keyword.

	var x = 5;
	x = 6; // compiles

** How does one borrow a value in an expression?  x = borrow y;
	
Function arguments are immutable variable bindings by default.  To make them mutable, use the `var` keyword again.

	public SomeFunction(x: int, var y: int) -> void
	{
		// x is immutable, y is mutable
	}

All variables are references to values (actually, pointers and `ref` are exceptions to this).  There are no primitive types or "value" types.  But don't worry about performance, the compiler is really good at optimizing away most references. A mutable variable binding allows a variable reference to be mutated to reference a different value.

	var c = new Customer("Alice");
	c = new Customer("Bob");

However, a mutable binding still doesn't mean the value can be mutated.

	var c = new Customer("Tom");
	c.Age = 25; // compilation error

By default, references are immutable just like variable bindings.  However, you can make a reference mutable using the `mut` keyword.

	let c: mut = new Customer("Tom");
	c.Age = 25; // compiles
	
The above variable declaration is shorthand for `let c: mut Customer = new Customer("Tom");`.  It is not necessary to write all that out, because the compiler uses type inference to deduce the full type of the variable `c`.

By default a variable has ownership of its reference.  If you assign it into another variable, the reference will *move*.

	let x = 5;
	let y = x;
	console.WriteLine(x); // compilation error: x has moved

After a reference is moved, it can't be used anymore.  That might sound rather inconvenient, but by default all arguments are passed as *borrow references*.  A borrow reference temporarily borrows the reference.  Again the default is for immutable borrow references, but there can also be mutable borrow references.

	let c: mut = new Customer("Karen");
	let x = TotalDollarsSpent(c); // borrows customer
	// TODO string interpolation? formatting? 
	console.WriteLine("Customer '{c.Name}' spent {x:C}"); // compiles because `c` was borrowed not moved
	
For a function to mutably borrow it must declare that it does so and you must indicate the mutable borrow with `mut` when calling the function.

	let c: mut = new Customer("Cam");
	DeleteOrders(mut c); // mutably borrows `c` so it can modify orders
	console.WriteLine("Customer '{c.Name}` has {|c.Orders|} orders"); // compiles because `c` was borrowed not moved
	
To move a reference into a function it must declare that it does so and you must indicate the move with `move` when calling the function. **Is move a better keyword than own?**

	let x = 5;
	let y = Square(move x); // moves the reference
	console.WriteLine(x); // compilation error: x has moved

At any given time there can be only one reference with ownership of an object whether it is mutable or not.  There can be any number of immutable borrow references to an object, but there can be no mutable references when there are.  A mutable reference that is immutability borrowed becomes immutable until the borrow ends.  There can be only one mutable borrow reference to an object.  The owning reference becomes unusable until the mutable borrow is returned.

If you have an immutable borrow, you can make more immutable borrows.  However, if you have a mutable borrow, then passing that moves the borrow.
	

----------------------

	var x: mut = new Foo(1);
	let y;
	{
		let y = x..Borrow(); // immutable borrow
		console.WriteLine("{x} {y}"); // Can use x like an immutable borrow
		let z = y;
		console.WriteLine("{x} {y}"); // Can still use y like an immutable borrow
		x = new Foo(2); // Compile error, can't end Foo(1) while it is borrowed
		y = x; // Move x to y. Borrows still valid so ok?
	}

# Lifetimes

Each variable binding has a lifetime determined by the scope it is declared in.  The lifetimes of variables end in the opposite order they were declared in.  All values have a lifetime.  However, the lifetime of a value may not be limited to the lifetime of one variable since ownership of the value could be moved to another variable, field or even returned.  When the lifetime of a variable that has ownership of a value ends, that value will be deleted and any destructor will be called.

	let y;
	{
		let z = 6;
		let x = 5;
		console.WriteLine(x + z);
		y = z;
		// lifetime of x ends, since x has ownership of value 5, its lifetime ends
		// lifetime of z ends, since the value has moved to y, it no longer has ownership, the lifetime of the value 6 continues
	}
	console.WriteLine(y);
	// lifetime of y ends, since y has ownership of value 6, its lifetime ends

Each borrow has a lifetime.  The lifetime of all borrows must end before the lifetime of the value ends.  The lifetime a reference must end before the lifetime of the variable it references.

Ownership for local variables is implicit.  Indeed, a variable could go from owning a value to borrowing another. In that situation the lifetime of the value ends and it is destructed.

	let x = 5; // x owns 5
	x = |collection|; // x borrows cardinality of collection.  lifetime of value 5 ends

The situation is somewhat different for parameters and fields.

## Parameters

A parameter must explicitly either borrow or take ownership of its parameter.  After which, it becomes a regular local variable and the ownership could change.  By default parameters borrow their argument.  For a parameter to take ownership of its argument, it is marked with `move`.

	// Here `console` and `visitor` are borrowed, ownership is taken for `customer`
	public SomeFunction(console: mut Console, customer: move Customer, visitor: Customer) -> void
	{
		console.WriteLine(customer.Name);
		customer = visitor; // lifetime of original Customer object ends, customer now borrows visitor
		console.WriteLine(customer.Name);
	}

## Fields

Options:

	1. Ownership is explicitly stated with 'owns' and 'borrows'
	2. Ownership is implied by attaching a lifetime
	3. Ownership is tracked with a flag
	
The problem with option three is that upon entering a method, we don't know if any given field is owned or not.  So we don't know whether when we read out of it we need to move the value out or not.

Note in standard Rust, you must own a value in order to move out of its fields.  And it checks that all fields hold a value before the method exits.  Not sure how that safely manages cross thread issues.

For certain types, like Buffer, Array and List we want at least the option of choosing whether they own or borrow via generics.

Examples:

	1. List<T> - want List<T> to default to owned mutable?
	2. Maybe<T> - want Maybe<int> to default to owned immutable
	3. Iterator<T>/Enumerator<T>
	4. Comparable<T>
	5. Visitors - want to be able to specify borrow/move of parameters, but that doesn't apply to throws?
	6. Numeric functions - may want T to not include lifetime information

Idea:

Lifetime parameters are prefixed with tilde `~`.  Other generic parameters can always have lifetimes specified?  You can prefix variable names with the tilde to refer to the lifetime of that variable.

# Static Lifetime

The special lifetime `~static` indicates that a reference lives the length of the entire program and will never be deleted.  I think that a static reference should be allowed to be converted to an `own` reference if the class is immutable.  This will require something like a drop flag (or tagged pointer) to track whether the owned reference should be deleted.  This is needed to allow a function like:

	public DisplayValue(x: int) -> own string
	{
		return int.TryParse(x) ?? "(null)";
	}

Here, the lifetime of the string constant is `~static` and the reference returned by `TryParse` is owned.

**Problem: if own allows you to recover mutability then you could gain mutability on static data that shouldn't be mutable**

# Owned borrows

There may be situations where a class wants to borrow something, but we want to actually give it ownership.  For example, `string` is easiest expressed as borrowing the UnsafeArray it contains.  That allows substring to work correctly.  However, there will need to be strings that own their UnsafeArray.

# Owned as Lifetime

It is almost as if owned could be a special lifetime that is shorter than static but longer than any fixed lifetime since it can be passed around by the thing that owns it.

# Child Lifetime


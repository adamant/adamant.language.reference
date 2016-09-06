# Reference Types

There are two kinds of types in Adamant, reference types and value types.  Reference types are so named because they are passed by reference.  Value types are as one would expect passed by value.  In this section, we'll explain reference types because most of the types you create in Adamant will be reference types.  Later we'll explain value types.

In languages like C#, all reference types are stored on the heap with pointers to them on the stack or in other object. All value types, on the other had, are stored direct on the stack or in the object declaring them.  That is not the case in Adamant.  The ownership system gives the Adamant compiler a lot more information about how memory is used in your program.  As a result, it is able to optimize many uses of reference types to store them directly on the stack or in the referencing object.  Just because a reference type has to be placed on the heap in one usage, doesn't mean it is on the heap everywhere it is used either.  This is an important feature of Adamant as it allows you to worry about what your app does rather than where objects are stored while being confident the best storage choice is being made.  It also means that a minor change necessitating a change in storage is a minor change rather than a big project of changing all uses of the value.

Any type declared as a class in Adamant is a reference type.  Let's consider a simple reference type for points.

	public mut class Point
	{
		public var X: int;
		public var Y: int;

		public new(x: int, y: int)
		{
			X = x;
			Y = y;
		}
	}

If you've read the section on [variable bindings](variable-bindings.md) and are familiar with other object oriented languages, this should be pretty clear to you.  The declaration that looks like a function except with the `new` keyword instead of a name is how a constructor is declared in Adamant.  It let's us create a point given values for `X` and `X`.  You can see the `X` and `Y` values are mutable.  The `int` type is a value type, but don't worry about that for now.  They behave like you would expect.

## Reference Ownership

When a new object is created, the first variable it is placed in has *ownership* of the reference.

	let a = new Point(4, 3);
	console.WriteLine("a = ({a.X}, {a.Y})");

Here the `a` variable owns the reference to the `Point` object.  When `a` goes out of scope, the point will be automatically released.

## Borrowing

When a reference is passed to another variable or function, it is *borrowed*.  A borrowed reference is not responsible for releasing the value it references.

	public DistianceFromOrigin(p: Point) -> float
	{
		return sqrt<float>(p.X*p.X + p.Y*p.Y);
	}

	// In Main
	let a = new Point(4, 3);
	console.WriteLine("a = ({a.X}, {a.Y})");
	let distance = DistianceFromOrigin(a);
	console.WriteLine("distance from origin {distance}");

Here the `DistianceFromOrigin` borrows a reference to point referenced by `a`.  By default, borrowed references are immutable.  They can't be used to mutate the object being referenced. If we tried to add `p.X *= 2` as the first line of `DistianceFromOrigin` we would get a compile error saying that `p` can't be mutated.

## Mutable Borrowing

It is also possible to borrow a reference to a mutable object. To do this use the `mut` keyword when passing the value.  If taking a mutable borrow with a function parameter, simply declare the parameter as `mut`.

	let a = mut new Point(4, 3);
	{
		let b = mut a; // b: mut Point
		b.Y = 6;
	}
	console.WriteLine("a = ({a.X}, {a.Y})");

This will print "a = (4, 6)" because the point object is mutated through the `b` reference to it.  Mutable borrowing is different from immutable borrowing. As an example, consider:

	let a = mut new Point(4, 3);
	let b = mut a; // b: mut Point
	console.WriteLine("a = ({a.X}, {a.Y})");
	b.Y = 6; // mutate the point

In this example, you'd get a compile error on the third line saying `a` could not be immutably borrowed because it was already mutably borrowed.  It turns out there are rules to borrowing, but before we get to those, let's talk about handing off ownership.

## Moving References

We've seen how references can be borrowed both immutably and mutably.  However, sometimes one needs to actually transfer ownership of a reference to change who is responsible for releasing the object.  To do this, use the special lifetime `~own` and the `move` keyword.  Lifetimes are explained in detail later.  Every reference has a lifetime, and the lifetime of all references that own their value is the special lifetime `~own`.  In all the examples above, the own lifetime has been inferred by the compiler, so it wasn't necessary to type it.

Imagine we need a function that will use up an object some how.  Say it will read the rest of and close a file.  Then it will want to take responsibility for releasing that object.  Of course, any such example for points will be a little contrived, but here goes.

	public Consume(console: mut Console, p: ~own Point) -> void
	{
		console.WriteLine("consumed ({p.X}, {p.Y})");
	}

	// In Main
	let a = new Point(4, 5);
	Consume(mut console, move a);

Here we declare the `Consume` function as taking ownership of the point it is passed by declaring the lifetime as `~own`.  When we call the consume function we must explicitly indicate that we are passing off ownership of the point using `move`.  This makes the intent of the code very clear to anyone who might read it in the future.

Once a reference has been consumed it can no longer be used by its original owner.

	let a = new Point(4, 5);
	Consume(mut console, move a);
	Consume(mut console, move a); // compile error

The second call to consume will not compile, giving an error that the reference in `a` has been moved out of `a`.

When passing ownership, mutability works a little differently.  When taking ownership of a reference, we can change its mutability.  This is safe because we are the only ones with a reference to it and we own it, it is ours now to do whatever we want with.  Unlike borrowing, where mutability is declared when passing a value, mutability is declared where ownership is received.

	public Transform(p: ~own mut Point) -> ~own Point
	{
		p.X *=2;
		p.Y *=3;
		return move p;
	}

	// In Main
	let a = new Point(4, 5);
	let b = mut Transform(move a);
	b.X -= 1;

Notice that `a` is immutable and when calling `Transform` we move the reference without indicating it is mutable.  However, the `Transform` makes the point mutable when it takes ownership of it.  When `Transform` returns the reference, it does so as immutable (Note: emit compiler warning for `mut` placed in returned ownership).  However, we want to mutate the result so we use `mut` in front of the call to `Transform`.  Any time something is returning ownership, the mutability is determined by the destination.  Using `mut` on the result is just shorthand.

	// This code
	let a = mut new Point(4, 5);
	let b = mut move a;
	let c = mut Transform(move b);

	// Is Equivalent to
	let a: mut = new Point(4, 5)
	let b: mut = move a;
	let c: mut = Transform(move b);

## Borrowing Rules

Here are the rules for ownership and borrowing in Adamant.

  1. Only one reference can have ownership of an object at a time.
  2. You must have ownership to transfer ownership
  3. No borrows can exist when transferring ownership.
  4. Any borrow must last for a lifetime less than that of the reference being borrowed from.
  5. You may have one or the other of the two kinds of borrows, but not both at the same time.
     * One or more immutable borrows of an object
	 * Exactly one mutable borrow of an object

These rules are enforced by the compiler.  The portion of the compiler responsible for this is often called, the *borrow checker*. You may notice these rules match those needed for safe current access to data.  That is how Adamant provides data race safety through its ownership system.

## A Second Look at Borrows

Let's consider again an example from earlier.

	let a = mut new Point(4, 3);
	let b = mut v; // b: mut Point
	console.WriteLine("a = ({a.X}, {a.Y})"); // compile error
	b.Y = 6; // mutate the point

Now that we no the rules of borrowing, we can see that the second line makes a mutable borrow of the point in `b`.  On the third line, the borrow `b` is still alive because it will be used on the fourth line.  But the call to `WriteLine` needs to borrow `a` immutably in order to read the values of `X` and `Y`.  This isn't allowed at the same time, so we get a compiler error.

The code that did compile was:

	let a = mut new Point(4, 3);
	{
		let b = mut a; // b: mut Point
		b.Y = 6;
	}
	console.WriteLine("a = ({a.X}, {a.Y})");

Here the lifetime of `b` clearly ends before the call to `WriteLine` since `b` goes out of scope at the end of the block surrounded with curly braces.

## Issues Ownership and Borrowing Prevent

Why have these rules around ownership and borrowing and add the complexity of move?  Well, these rules provide safety for concurrency and other issues including iterator invalidation and use after free. 

### Iterator Invalidation

TODO write section

### Use After Free

References must not last longer than the resource they refer to.  Adamant will check the uses of references to ensure that this is true.  If this rule were not enforced, we could use an invalid reference.  In manual memory management this is known as 'use after free'. 

	let x: ref int;
	{
		let y = 85;
		x = ref y;
	}

	console.WriteLine("{x}");

When we try to compile this, we get an error because `y` does not live long enough for `x` to reference it.

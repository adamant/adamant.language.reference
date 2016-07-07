# Value Types

We have covered value types which are the majority of types you will deal with in Adamant.  However, there are also value types which are important to understand as many of the predefined types in Adamant are value types.  Again a value type is a type that is passed by value.  So they always reside on the stack or directly in the object holding them.  When they are passed between functions they are moved or copied.  For both of those reasons, it is a good practice to limit the amount of memory used by a value type.  In Adamant, any type declared as a `struct` is a value type.  In general, you should reach for a `class` first and only use a `struct` when your particular scenario fits the few situations they are good for.  It's important to note that, just because you pass a struct by value doesn't mean a copy of the object is happening.  In many important cases, the compiler is able to optimize those copies out of existence.

## Move Semantics

Simple structs have *move semantics*.  That means rather than being borrowed like reference types, the value always moves.  The equivalent for reference types would be if we used `move` whenever passing it.

	public mut struct ChannelReceiver
	{
		public let BufferSize: int;

		public new(bufferSize: int)
		{
			BufferSize = bufferSize;
		}
	}

	// In Main
	let a = new ChannelReceiver(83);
	let b = a; // implicit move
	console.WriteLine("b.BufferSize = {b.BufferSize}");
	console.WriteLine("a.BufferSize = {a.BufferSize}"); // complier error

The last line give a compiler error stating that the value has been moved out of `a`.  We see that when we assigned `b = a` the value was moved just as if we had used the `move` keyword.  The same thing happens when passing a struct to a function.

Structs with move semantics aren't used very often.  The most common use is in [pseudo references](pseudo-references.md), where they are combined with other features to make something that feels more like a reference type.  Instead, most value types have copy semantics.

## Copy Semantics

Most value types have *copy semantics*.  We're already familiar with a type that has copy semantics.  The `int` type we've been using all along is an example of a struct with copy semantics.  To see how to create our own types with copy semantics, let's make a simple complex number `struct`.

	public struct complex
	{
		public let Real: int;
		public let Imaginary: int;

		public new(real: int, imaginary: int)
		{
			Real = real;
			Imaginary = imaginary;
		}

		public implicit new copy(from: ref complex)
		{
		}
	}

	// In Main
	let a = new complex(6, 4);
	let b = a;

	console.WriteLine("b = {b.Real}+{b.Imaginary}i");
	console.WriteLine("a = {a.Real}+{a.Imaginary}i");

We did the same thing we did with our `foo` struct, but this time it compiles and runs, outputting:

	b = 6+4i
	a = 6+4i

We see that `b` became a copy of `a`.  How did that happen?  It's because of the special *copy constructor* we declared.  Copy constructors are declared with `new copy`.  The `implicit` keyword in front tells the compiler to go ahead and call the copy constructor whenever a copy of the struct is needed.  That gives this struct copy semantics instead of move semantics.  If we really wanted to, we could still explicitly move a value though.

	let a = new complex(6, 4);
	let b = move a; // note the use of `move`

	console.WriteLine("b = {b.Real}+{b.Imaginary}i");
	console.WriteLine("a = {a.Real}+{a.Imaginary}i"); // complier error

Now we get the same compiler error as before stating that we can't use `a` because the value has been moved out of it.  The `move` keyword has just the same effect on copy types as it does on reference types.

You're probably still wondering how the copy happened since we didn't write any code in the copy constructor.  Since all the fields of our struct were themselves copyable, the compiler wrote the code to copy them for us.  If any had not been implicitly copyable, we would have needed to write code to copy them.

You may also be wondering about that `ref` in front of the type of the copy constructor parameter.  Since copy types cause a copy when they are passed, but we are defining how to copy this structure, we need to avoid a copy being made.  Otherwise, there would be an infinite loop.  So we have to use a reference to the parameter.  [Variable references](variable-references.md) are explained in detail later.

Notice that we made our complex struct immutable rather than mutable like our Point class earlier.  In general, copy types should be immutable.  Mutable copy types can be very confusing to people using them because it is easy to accidentally make a copy and mutate the copy when you mean to mutate the value.

## Explicit Copy

It doesn't make sense to give copy semantics to some structs, but we still want to be able to copy them when needed.  For example, if we wanted to make our complex struct mutable, then it would make sense to give it move semantics, but be able to explicitly copy it.  This can be done simply by declaring the copy constructor as `explicit` instead of `implicit`.

	public mut struct Complex
	{
		public var Real: int;
		public var Imaginary: int;

		public new(real: int, imaginary: int)
		{
			Real = real;
			Imaginary = imaginary;
		}

		public explicit new copy(from: ref Complex)
		{
		}
	}

	// In Main
	let a = mut new Complex(6, 4);
	let b = new copy(ref a); // explicit copy
	a.X = 4;

	console.WriteLine("b = {b.Real}+{b.Imaginary}i");
	console.WriteLine("a = {a.Real}+{a.Imaginary}i");

This outputs:

	b = 6+4i
	a = 4+4i

Notice that `b` is a copy of `a`.  We were able to mutate `a` without affecting `b`.  The copy didn't happen automatically though, we had to explicitly ask for a copy to be made.  If we hadn't the value would have moved from `a` to `b` and we couldn't have used `a` anymore.  Calling a copy constructor is different from calling other constructors.  Where we would normally give the type we are constructing we use the `copy` keyword instead.  The type we are constructing matches the type we are copying.  Because the argument to the copy constructor is a reference, we have to take a reference to `a` using the `ref` keyword.

If you're observant you may have noticed that we declared `a` as `let` and `mut`, just like it was a reference type.  A mutable value type will let you change fields and call methods requiring a mutable value.  Declaring a value type with `var` will let you assign a different variable to the var.

Classes can have explicit copy constructors too.
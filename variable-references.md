# Variable References

So far, we have seen reference types and value types.  In the section on copy types we saw the use of the `ref` keyword, but didn't explain it.  In this section we will explore the `ref` keyword and the types associated with it.  They are *variable reference* types.  A variable reference is a reference to a variable binding rather than to a value.

## Immutable Variable References

Sometimes we want to pass a value type to a function, but we don't want to move it and we don't want to incur the potential cost of copying it.  We can do that by passing a reference to the variable.

	public AbsoluteValue(x: ref complex) -> float
	{
		return sqrt<float>(p.Real*p.Real + p.Imaginary*p.Imaginary);
	}

	// In Main
	let a = new complex(6, 4);
	let abs = AbsoluteValue(ref a);

	console.WriteLine("a = {a.Real}+{a.Imaginary}i");
	console.WriteLine("|a| = {abs}");

Here we declared the `AbsoluteValue` value to take a reference to a complex.  That means the function will take a reference to a variable holding a value of type `complex`.  When we call the function we need to take the reference of the value we want to pass using the `ref` keyword.  This way the value in `a` is not copied.  The copy constructor will not be called in this example.

Just because we have a reference to an immutable variable, doesn't mean we can't change our reference to a different variable.

	let a = new complex(6, 4);
	let b = new complex(7, 2);
	var x = ref a; // x: ref complex
	console.WriteLine("x = {x.Real}+{x.Imaginary}i");
	x = ref b;
	console.WriteLine("x = {x.Real}+{x.Imaginary}i");

Outputs:

	x = 6+4i
	x = 7+2i

Notice we declared `x` with `var` meaning that even though the referenced variable couldn't be mutated, we can mutate which variable it references.  Just like we can use `var` with reference types to allow us to change which immutable object a variable references.

## Mutable Variable References

We can also use variable references when we want to mutate the value of a variable.

	public Double(x: ref var complex) -> void
	{
		x = new complex(x.Real*2, x.Imaginary*2);
	}

	// In Main
	var a = new complex(6, 4);
	console.WriteLine("a = {a.Real}+{a.Imaginary}i");
	Double(ref var a);
	console.WriteLine("a = {a.Real}+{a.Imaginary}i");

Outputs:

	a = 6+4i
	a = 12+8i

Here we indicate that we want a reference to a mutable variable by declaring our parameter `ref var`.  Notice that we are referring to the mutability of the variable rather than the value so we use `var` instead of `mut`.  When we call the function, we must pass a reference to a mutable variable using `ref var`.  If `a` were immutable, having been declared with `let`, then we would not be allowed to take a mutable reference to it.

Even with a reference to a mutable value, we can still mutate which variable we are referencing.

	let a = new complex(6, 4);
	let b = new complex(7, 2);
	var x = ref var a;  // must be declared `var`, x: ref var complex
	Double(x); // don't need `ref var` because `x` already is one
	console.WriteLine("x = {x.Real}+{x.Imaginary}i");
	x = ref var b;
	console.WriteLine("x = {x.Real}+{x.Imaginary}i");

Outputs:

	x = 12+8i
	x = 7+2i

## Variable References to Reference Types

So far, we've seen how you can use references to variables holding value types.  However, you can also make references to variables holding reference types.

	let a = new Point(1, 1);
	let b = new Point(2, 2);
	var p = ref a; // p: ref Point
	console.WriteLine("p = ({p.X}, {p.Y})"); // prints 'p = (1, 1)'
	p = ref b;
	console.WriteLine("p = ({p.X}, {p.Y})"); // prints 'p = (2, 2)'

	let c = mut new Point(3, 3);
	let q = ref mut c; // q: ref mut Point
	q.X += 1;
	console.WriteLine("q = ({q.X}, {q.Y})"); // prints 'q = (4, 3)'

Of course, taking a reference to an immutable variable holding a reference, even a reference to a mutable object isn't very useful.  It usually makes more sense to directly borrow the reference. Indeed, the compiler will warn any time you declare something to be a reference to an immutable variable holding a reference type.  However, references like this do come in handy for generic types.

It is more useful to have a reference to a mutable variable holding a reference type.

	public abstract class animal
	{
		public abstract Speak() -> string;
	}

	public class cat: animal
	{
		public override Speak() -> string
		{
			return "Meow!";
		}
	}

	public class dog: animal
	{
		public override Speak() -> string
		{
			return "Woof!";
		}
	}

	public Replace(x: ref var ~own animal)
	{
		x = new Cat();
	}

	var pet = new Dog(); // pet: animal because it is passed to Replace()
	console.WriteLine(pet.Speak()); // prints "Woof!"
	Replace(ref var pet);
	console.WriteLine(pet.Speak()); // prints "Meow!"

By taking a `ref var Animal` the `Replace` function was able to change which object the `pet` variable referenced.  The `~own` is a [lifetime](lifetimes.md) declaration needed in this situation. 

And of course, we can even make mutable variables holding references to mutable variables referencing mutable objects.

	var a = mut new Point(9, 4); // a: mut Point
	var p = ref var a; // b: ref var mut Point
	p.X = 6;
	console.WriteLine("a = ({a.X}, {a.Y})"); // prints 'a = (6, 4)'
	p = new Point(0, 0);
	console.WriteLine("a = ({a.X}, {a.Y})"); // prints 'a = (0, 0)'
	var b = mut new Point(-1, -1);
	p = ref var b; // doesn't change a
	console.WriteLine("a = ({a.X}, {a.Y})"); // prints 'a = (0, 0)'

Notice the type of `p` is `ref var mut Point`.  Now you know why `var` is used for references to mutable variables rather than reusing the keyword `mut`.  Otherwise, the types `ref var Point` and `ref mut Point` wouldn't be distinguishable.

## Immutable is Still the Default

Just like with the mutability of values, we must always explicitly declare the mutability of variable references.

	var a = mut new Point(5, 2); // note `var` and `mut`
	let p = ref a; // p: ref Point
	p.X = 4; // compile error: object referenced by `p` is immutable
	p = new Point(6, 8); // compile error: variable referenced by `p` is immutable

Even though the variable `a` being referenced is declared `var` and references a `mut` point, they can't be mutated through the reference `p` because it has the type `ref Point`.

## Rules for References to Variables

References to variables act just like borrows.  There can be only one mutable reference, or one or more immutable references.

	var a = new complex(1, 1);
	let x = ref var a;
	{
		let y = x; // borrows away the reference to a
		console.WriteLine("x = {x.Real}+{x.Imaginary}i"); // compile error: can't use x here
		y = new complex(2, 2);
	}
	// now that `y` has gone out of scope we can use `x` again
	console.WriteLine("x = {x.Real}+{x.Imaginary}i"); // prints 'x = 2+2i'

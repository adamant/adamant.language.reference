# Tuples

Part of me doesn't like tuples because their individual values are unnamed and that feels hacky and lazy.  However, if I have generic type lists `class Something<T...>` then tuples are basically easily hand implemented in the language by `Tuple<T...>` so you know it will be added.  Also, lists of types basically are tuples.  We will probably need tuples in order to refer to values of type lists.  In many other languages tuples have a syntax like `(x,y)` but that seems easily confused with grouping.  Also, there is no good syntax for a tuple of one value.  Some languages use `(x,)` which just seems ridiculous.  Following the same logic as arrays, if everything is supposed to be a reference, then so should tuples and they should be constructed with new.  Hence the syntax could be something like `new (x,y)`, but that could be confused with the object literal syntax.  I think tuple fields should be mutable like array elements.

Then there is the question of how to access the elements.  It could be like arrays `tuple[1]` but that could be confusing and might encourage people to define constants for the various parts of tuples.  Other languages use `tuple.1` which isn't horrible, but feels strange.  However, I think they avoid that with destructuring assignment.

## Syntax

Since there are no primitive arrays only `Unsafe_Array<T>`, then `[]` could be used for tuples. That might confuse people, but would really work out well.

## As Type Lists

For visitors and other cases I had the idea of using type lists which should probably just be tuples.  That means I need ways of talking potentially destructuring tuples.

	public class Something_Visitor<TArgs: Tuple, TReturn, TThrows: Tuple>
	{
		public Visit(host: Something, args: TArgs...) -> TReturn
			throws TThrows...
		{
		}
	}

There is a problem with this syntax.  That should mean `TArgs` is a tuple value, not a tuple type.

If ... means to break apart the tuple into listed things, it seems like you should be able to call a function as `Func(tuple...)` and have it break apart the tuple.  Note here, the generic types are passed like `new Something_Visitor<[int,int],int,[void]>()`.  If one wants a type that allows arguments to be passed inline then do:

	public class My_Tuple<T...> // See C++ variadic templates
	{
	}

Here we compute with type parameters

	public Factorial<N:uint>() -> uint
	{
		return N*Factorial<N-1>() -> uint
	}

	public Factorial<N:0>() -> uint // or would Factorial<=0> be a better syntax?  Can't be Factorial<N=0> because that looks like a default type 
	{
		return 1;
	}


# Optional Types

Optional types are used to indicate situations where a value may not be present. They replace the role of null pointers and null references in languages that support them. Handling of optional types is highly optimized and using an optional type should never be a performance concern.

## The `none` Value

To express that an optional type has no value, use the `none` keyword.

## Conditioning on a Value

TODO: Not sure the preferred way to do this.  Listing options here.

	let x: int? = ...;

	if let y? = x
	{
		// y is the value of x
	}

	match x
	{
		y? => ...,
		none => ...,
	}

	if x != none
	{
		// should work, but no good way to work with value of x
	}

	if x? // an "is none" operator
	{
		// x is not none
	}

## Coalescing Operator

The coalescing operator `??` allows for the replacement of `none` with another value.  If the expression to the left of the operator evaluates to a value other than `none` then the result of the coalescing operator is that value and the right hand expression is not evaluated.  Otherwise, the result is the result of evaluating the right hand expression.

## Conditional Access

TODO should there be C# style `x?.y` and `x?[y]` operators or should that be implicit.

## Operator Lifting

Operators are lifted for optional types similar to how they are in C#.

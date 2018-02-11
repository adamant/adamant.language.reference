# Operator Overloading

Operators are essentially methods with special names. One just declares them with the `operator` keyword. However, there are a few wrinkles because some operators work in very unique ways. The following operators can be overloaded.

Operators | Overloadability
--- | ---
`+` `-` `not` `?` | These unary operators can be overloaded
`+` `-` `*` `/` `%` `xor` `.` | These binary operators can be overloaded
`==` `<>` `<` `>` `<=` `>=` | The comparison operators can be overloaded in pairs
`and` `or` | These operators require special handling when overloading
`??`  | The coalescing operator requires special handling
`+=` `-=` `*=` `/=` `%=` | These operators can be overloaded for greater efficiency
`=` `->` `f(x)` `as` | These operators can't be overloaded

To make an operator overload generic, place the type parameters after the `operator` keyword.

For the below examples, these types will be used.

	public struct complex
	{
		public let RealPart: float;
		public let ImaginaryPart: float;

		public new(realPart: float, imaginaryPart: float)
		{
			RealPart = realPart;
			ImaginaryPart = imaginaryPart;
		}
		public implicit new copy(other: ref Self) { }
	}

	public struct fuzzy_bool
	{
		public let Value: float;

		@pre value >= and value <= 1
		public new(value: float)
		{
			Value = value
		}
		public implicit new copy(other: ref Self) { }
	}

Note: Operators are not tied to interfaces the way they are in Rust because that would imply that they have universal semantics. That is, that every use of the operator is meant to have the same semantics. However, just like method names, the same name can have two different meanings in different contexts.

## Unary Operators

The unary operators can be overloaded.

	// In `complex`
	public operator +(ref self) -> complex
	{
		// return a copy, unchanged
		return self;
	}

	public operator -(ref self) -> complex
	{
		return new complex(-RealPart, -ImaginaryPart);
	}

	// In `fuzzy_bool`
	public operator not(self) -> fuzzy_bool
	{
		return new fuzzy_bool(1 - Value);
	}

	public operator ?(self) -> bool
	{
		return Value == 1;
	}

Note: `?` does not have to return null.

## Binary Operators

The binary operators can be overloaded.

	// In `complex`
	public operator +(ref self, other: ref complex) -> complex
	{
		return new complex(RealPart + other.RealPart, ImaginaryPart + other.ImaginaryPart);
	}

Interoperating with existing types can require extending them.

	// In `complex`
	public operator +(ref self, other: float) -> complex
	{
		return new complex(RealPart + other, ImaginaryPart);
	}

	// Outside `complex`
	public extend struct float
	{
		public operator +(self, other: ref complex) -> complex
		{
			return new complex(self + other.RealPart, other.ImaginaryPart);
		}
	}

## Comparison Operators

The comparison operators can be overloaded like other binary operators. However, they must overloaded in pairs.  When one of the operators in pair is overloaded, the other one in the pair must be overloaded to.

Operator | Pairs
--- | ---
`==` | `<>`
`>` |  `<`
`>=` |  `<=`

Note: The `obsolete` attribute can be used to mark a member of the pair as uncallable if it is explicitly desired not to implement the other member of the pair.

## Logical Operators

The logical operators `and` and `or` are special because they perform short circuit evaluation.  That is, there second argument may not be evaluated.  To support this, each requires two overloads.  The first determines when the second argument isn't evaluated, the second provides the value when it is.  The first must return an optional type.  If the result is `none` then the second argument will be evaluated and the binary form called. If one form is implemented, the other is required.  When overloaded for some type, `x and y` is effectively evaluated as `x.and() ?? x.and(y)` and `x or y` is effectively evaluated as `x.or() ?? x.or(y)`. Note the first argument will only be evaluated once.

TODO: one actually may want to transform the value before passing to the second function.  Perhaps the result needs to be some other type that allows a value to be passed in the "none" case?

	// In `fuzzy_bool`
	public operator<TOther> and(self) -> fuzzy_bool?
		where TOther: fuzzy_bool
	{
		return if Value == 0 => self else => none;
	}

	public operator and(self, other: fuzzy_bool) -> fuzzy_bool
	{
		return new fuzzy_bool(Value.Min(other.Value));
	}

	public operator<TOther> or(self) -> fuzzy_bool?
		where TOther: fuzzy_bool
	{
		return if Value == 1 => self else => none;
	}

	public operator or(self, other: fuzzy_bool) -> fuzzy_bool
	{
		return new fuzzy_bool(Value.Max(other.Value));
	}

Note: the generics are needed on the single argument form so we can get correct types.

## Coalescing Operators

Similar to the logical operators, the coalescing operator is evaluated in two steps. The first step determines whether the second argument should be evaluated and the second step determines the coalesced value. If one form is overloaded, the other is required to be overloaded. The first form must return an optional type.

TODO: one actually may want to transform the value before passing to the second function.  Perhaps the result needs to be some other type that allows a value to be passed in the "none" case?  In the code below, it would be the exception value.

	public struct Result<T, E>
		where E: Exception
	{
		public operator<TOther> ??(ref self) -> Result<T, CompositeException>?
			where TOther: Result<T, _>
		{
			return match self
				{
					Result.Ok[v] => new Result.Ok(v),
					Result.Error => none
				};
		}

		public operator<T, E2> ??(ref self, other: ref Result) -> Result<T, CompositeException>
		{
			// We already know self is Error, otherwise we wouldn't be called
			if let Result.Error[e1] = self
			{
				return match other
					{
						Result.Ok[v] => new Result.Ok(v),
						Result.Error[e2] => new Result.Error(new CompositeException(e1, e2));
					}
			}
			// TODO: better function for this
			Release.Assert(false, "Impossible case");
		}
	}

## Assignment Operators

These operators will be automatically generated if the corresponding binary operator is overloaded.  However, they can be directly overloaded for greater efficiency, different behavior or to mark them as uncallable.

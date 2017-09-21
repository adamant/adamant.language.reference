# Other Feature Ideas

This section describes a number of random ideas for the language that may or may not make it in, but are interesting enough to warrant description.

## Loop Else

Sometimes it is useful to execute some code if a loop is never run.  This can be done with the else clause of the while and for loop.

	while condition
	{
		// do work
	}
	else
	{
		// condition was false to start with
	}

	for let x in expression
	{
		// do work
	}
	else
	{
		// no items in collection
	}

This can be useful for definite assignment.  If the loop assigns a variable, it may be the case that the loop never runs and the variable may be unassigned.  However, you can assign the variable in the else clause to a reasonable default so that the variable will definitely be assigned after the loop.

Note: this is different from the python style loop else construct which runs as long as the loop completed successfully.

Note: Alternatively, a different keyword or group of keywords could be used for loop else.  Options include `otherwise`, `loop else`, `while else`, `for else`, or `if none`.

## Allow `!` at the End of Function Names

Scheme uses `!` at the end of functions to indicate they are mutating.  That might not make sense for Adamant where mutation is probably more common and less frowned on.  Rust uses `!` to indicate macros.  It is nice to have a clear distinction for macros, but the syntax really strike one as clearly indicating a macro.  Since `!` denotes divergent functions and is not used to mean "not" what if we allowed `!` at the end of function names and used it to indicate divergent functions?  Otherwise it might be obvious that execution will terminate at one.

## Extra Name Escapes

Currently escaped identifiers really just allow the use of keywords as identifiers.  However, they could be expanded to allow any Unicode sequence not containing whitespace (or control characters?).  Names containing spaces could be enclosed in double backtick

	let `2legit = 2;
	let ``a really long name with spaces in it`` = 5;

Identifiers with spaces would be really useful when naming tests.

Allowing identifiers beginning with numbers could cause issues if we want to use backtick to mark of special literals like dates and times

## Date and Time Literals

Add support for date and time literals.  Could be done using either the backtick or single quotes.

	// With backtick
	let x = `2016-06-06;

	// With quotes
	let y = '2016-06-06';

The first would not mix with expanding the backtick to allow anything as an identifier.

## Tuple Base Class

It probably makes sense to have all tuple types inherit from and abstract base class `Adamant.Predefined::Adamant.Predefined.Tuple`.  C# has them implement several interfaces about structural equality.

## `out`

I have `ref`, does it make sense to add a `out` keyword like C#?  Or does the ability to return tuples make `out` not useful enough?  They could be really useful with external functions (though, not sure how the memory would get cleaned up).

## `var` is `mut` for Value Types

Currently, `var` and `mut` have distinct meaning for value types.  It is assumed this will be needed to implement certain pseudo references.  Also, on struct constructors it makes sense to have a `ref mut self` parameter.  However, perhaps using `var` should always imply `mut` for mutable structs. Logically it seems if you can assign a new value you should be able to mutate the value.

## Owned borrows

There may be situations where a class wants to borrow something, but we want to actually give it ownership.  One way to implement string would be for `string` is to borrow the UnsafeArray it contains.  That allows substring to work correctly.  However, there will need to be strings that own their UnsafeArray.  If we could pass an owned reference where are borrow was expected, then that would be handled.  Of course, this may require pervasive drop flags adding complexity and hurting performance.

## Returns That Can't be Ignored

Part of the Midori project's safety came from not being able to ignore the return value of a function except by explicit statement.  This had the effect of ensuring that return codes and error results couldn't be ignored.  If my error model includes Result<void> then it is possible people could ignore that.  Perhaps there should be some special handling to say that certain types shouldn't be ignored when returned.

# Obsolete Feature Ideas

## `if not` and `while not`

*Obsolete Reason:* adoption of control flow requiring blocks but not parenthesis (Rust style) means that this feature doesn't really makes sense as it is already essentially supported because control flow statements don't have parenthesis.

Languages like Ruby have inverted control flow with `until(condition)` for `while(not condition)` and `unless(condition)` for `if(not condition)`.  In languages without that there is often a need for an extra set of parens which can be ugly and confusing (i.e. `if(not(a and b)) statement;`.  What if the language allowed a not outside the required parens?  So that could become `if not(a and b) statement;`.  Of course, if we switch to control flow not requiring parens this becomes a non-issue (i.e. `if not(a and b) { statement; }`).  Though there is some extra clarity when parens are required, because you know the not must apply to the whole condition, not just the first part of it.

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

There may be situations where a class wants to borrow something, but we want to actually give it ownership.  One way to implement string would be for `string` to borrow the `Unsafe_Array` it contains.  That allows substring to work correctly.  However, there will need to be strings that own their `Unsafe_Array`.  If we could pass an owned reference where are borrow was expected, then that would be handled.  Of course, this may require pervasive drop flags adding complexity and hurting performance.

## Returns That Can't be Ignored

Part of the Midori project's safety came from not being able to ignore the return value of a function except by explicit statement.  This had the effect of ensuring that return codes and error results couldn't be ignored.  If my error model includes `Result<void>` then it is possible people could ignore that.  Perhaps there should be some special handling to say that certain types shouldn't be ignored when returned.

## Units of Measure

It would be really good to be able to have good units of measure either directly in the language or as a really clean library.  This might be a useful place for an effect that says all code uses units of measure.

## `else match`

Allow a match to occur after an else.

## Alternative Pointer Syntax

While C/C++ have well established pointer syntax and Adamant is in that linage, it will use pointers much more rarely.  So it may be acceptable to change the pointer syntax for purposes of clarity or freeing up symbols. In terms of clarity, a postfix dereference operator may be clearer than a prefix one and would then combine well with the member access operator. In terms of freeing up symbols, the unary star operator and unary ampersand operators aren't actually that useful for anything else.

### `@` Means Address Of, `^` Dereferences

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `@T`
Pointer to mutable T   | `@mut T`
Address of a struct    | `@x`
Address of an object   | `@x`
Deference Points       | `x^`
Access Member          | `x^.y`

### `@` Means "at", `&` Means Address Of

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `&T`
Pointer to mutable T   | `&mut T`
Address of a struct    | `&x`
Address of an object   | `&x`
Deference Points       | `@x`
Access Member          | `(@x).y` ?

This is confusing with C++ reference syntax. Also, it doesn't provide a clean member access unless the dot operator auto-dereferences which I've never liked.

### `ptr<T>`

Given that pointers are meant to be rare, why not say they are a special predefined type like unsafe array?

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `ptr<T>`
Pointer to mutable T   | `ptr<mut T>`
Address of a struct    | `ptr.to(ref x)`
Address of an object   | `ptr.to(x)`
Deference Points       | ?? `@x`
Access Member          | ? `x.y`

Here the type overloads the dot operator.  This is consistent with the behavior of the dot with variable references.  There doesn't seem to be a good way to handle dereference here though.  Perhaps there is some operator that could be allowed to be overloaded.  That operator maybe should be allowed on variable reference types to get the underlying reference.

## Alternatives for Escaped Identifiers

Currently, backtick `` ` `` is used to escape identifiers and double backtick creates escaped string identifiers.

Font | Escaped Keyword | Escaped String
---- | --------------- | --------------
Monospace | `` `class ``  | ``` ``A name with spaces!`` ```
Proportional | \`class | \`\`A name with spaces!\`\`

However, this conflicts with the use of markdown in comments when referring to code.  While escaped identifiers are rare, it can still be confusing.  Unfortunately, preventing this would mean not being able to use the backtick as a symbol in the language.  Still, this might be worth it. Note in all these examples, that the single symbol form may be restricted to keywords and reserved words only.  So it may not be legal to use `` `hello``.  This can make the syntax less ambiguous. Possible syntaxes:

 * `^class` and `^^This is a test!^^` (exponent would be `**`)
 * `^class^` and `^This is a test^`
 * At sign `@class`, but what about names with spaces? Confusing around attributes
 * Square brackets `[class]` and `[This is a test!]` (this conflicts with tuple syntax, for example `let [x,y] = x` does that destructure or assign to the identifier `[x,y]`?)
 * `[[class]]` and `[[This is a test!]]`
 * `*class` or `class*`, and `**This is a test!**` (dereference could be caret `^` and exponent double caret `^^`) (Suffix asterisk makes it seem like the star of a footnote which I like) (Double star reads like bold or shouting though)
 * `--This is a Test!--`
 * `~~This is a Test!~~`
 * `'class` and `''This is a test!''` vs string `"This is a test!"` but in variable width font that is ''This is a test!'' vs string "This is a test!"
 * `'class'` and `'This is a test! but don't use apostrophes'`
 * `#class` and `##This is a test!##`

For some of these syntaxes, the symbol could be taken to mean "literal" and used as the prefix for literal strings.  However, it was suggested by Alex Burkhart that string escaped string identifiers could instead be created by prefixing strings with the same character. These strings would work like literal strings in terms of what characters they accept. Possible syntaxes for that are:

 * `#class` and `#"An identifier"`
 * `'class` and `'This is a test!'` and `'"A literal string\"`
 * `\class` `\"This is a test!"`  (the blackslash is used for escape sequences, i.e. it escapes from the current mode. In this case escaping from regular parsing of keywords and strings)  The blackslash could possibly be combined with other characters to create other escape like things, perhaps around macros.
 * `@class` `@"This is a test!"` (but this is confusing with attributes)
 * `*class` `*"This is a test!"`

Finally, perhaps using backtick isn't so bad. It is unlikely that a code snippet in markdown begins with a backtick.  If we could eliminate the double backtick by putting backtick in front of strings then code could always be surrounded by double backtick in markdown.

Font | Escaped Keyword | Escaped String
---- | --------------- | --------------
Monospace | `` `class ``  | `` `"A name with spaces!"``
Proportional | \`class | \`"A name with spaces!"

However, this seems like a very easy to overlook syntax.

# Obsolete Feature Ideas

## `if not` and `while not`

*Obsolete Reason:* adoption of control flow requiring blocks but not parenthesis (Rust style) means that this feature doesn't really makes sense as it is already essentially supported because control flow statements don't have parenthesis.

Languages like Ruby have inverted control flow with `until(condition)` for `while(not condition)` and `unless(condition)` for `if(not condition)`.  In languages without that there is often a need for an extra set of parens which can be ugly and confusing (i.e. `if(not(a and b)) statement;`.  What if the language allowed a not outside the required parens?  So that could become `if not(a and b) statement;`.  Of course, if we switch to control flow not requiring parens this becomes a non-issue (i.e. `if not(a and b) { statement; }`).  Though there is some extra clarity when parens are required, because you know the not must apply to the whole condition, not just the first part of it.

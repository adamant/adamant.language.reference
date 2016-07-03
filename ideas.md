# Other Feature Ideas

This section describes a number of random ideas for the language that may or may not make it in, but are interesting enough to warrant description.

## `if not` and `while not`

Languages like Ruby have inverted control flow with `until(condition)` for `while(not condition)` and `unless(condition)` for `if(not condition)`.  In languages without that there is often a need for an extra set of parens which can be ugly and confusing (i.e. `if(not(a and b)) statement;`.  What if the language allowed a not outside the required parens?  So that could become `if not(a and b) statement;`.  Of course, if we switch to control flow not requiring parens this becomes a non-issue (i.e. `if not(a and b) { statement; }`).  Though there is some extra clarity when parens are required, because you know the not must apply to the whole condition, not just the first part of it.

## Control Flow Requires Blocks not Parenthesis

Currently, control flow is C style where the expression portion must be surrounded with parens, but the statement can be any statement.  Instead, we could go Rust style where the expression does not require parens, but the statement must be a block.  That solves the dangling else issue and also makes loop else unambiguous (i.e. people won't think the loop else is for an if).

## Loop Else

Sometimes it is useful to execute some code if a loop is never run.  This can be done with the else clause of the while and for loop.

	while(condition)
		// do work
	else
		// condition was false to start with

	for(let x in expression)
		// do work
	else
		// no items in collection

This can be useful for definite assignment.  If the loop assigns a variable, it may be the case that the loop never runs and the variable may be unassigned.  However, you can assign the variable in the else clause to a reasonable default so that the variable will definitely be assigned after the loop.

Note: this is different from the python style loop else construct which runs as long as the loop completed successfully.

Note: this could be confusing to people who are not familiar with it since else binds to the nearest construct.  Something like:

	if(condition)
		while(keepLooping)
			// loop body
	else
		// meant to be else of the if, but will actually be the else of the while

That would not be a problem if curly braces were always required.  Alternatively, a different keyword or group of keywords could be used for loop else.  Options include `otherwise`, `loop else`, `while else`, `for else`, or `if none`.
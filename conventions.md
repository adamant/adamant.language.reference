# Conventions

## Naming Conventions

The following naming conventions are enforced by compiler warnings.

  * Mutable Class Names, Type Parameters: Mixed_Case_With_Underscores
  * Immutable Class Names (including attributes): snake_case
  * Namespaces, Functions, Public Fields, Static Variables: PascalCase case
  * Parameters, Private & Protected Fields: camelCase

## Curly Brace Placement

Curly braces should almost always be on their own line, never K&R style with the open brace on the same line.  However, for control flow statements with a single statement, the braces and statement can appear on a single line indented.  If the single statement is short it may appear on the same line as the control flow.  This should generally be for single keyword statements or returns of a single variable (not a more complex expression).

	for x in 1..10
	{
		if f(x)
			{ doSomething(x); }
		if bar { break; }
	}
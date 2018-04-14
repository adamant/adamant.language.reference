# Conventions

## Naming Conventions

The following naming conventions are enforced by compiler warnings.

| Item                | Convention                              |
| ------------------- | --------------------------------------- |
| Packages            | `dotted.camelCase`                      |
| Namespaces          | plural `camelCase`                      |
| Types               | `Pascal_Case_With_Underscores`          |
| Type Parameters     | concise* `Pascal_Case_With_Underscores` |
| Functions           | `snake_case()`                          |
| Divergent Functions | `ALL_CAPS()`                            |
| Local variables     | `snake_case`                            |
| Fields              | `snake_case`                            |
| Constants           | `PascalCase`**                          |
| Global variables    | `ALL_CAPS`                              |
\* usually single uppercase letter: `T`<br>
\*\* Acronyms are capitalized and run together: `ScreenDPIToPrint`

## Curly Brace Placement

Curly braces should almost always be on their own line, never K&R style with the open brace on the same line. However, for control flow statements with a single statement, the braces and statement can appear on a single line indented. If the single statement is short it may appear on the same line as the control flow. For `break`, `continue` and `return` this should be an if expression.

```adamant
for x in 1..10
{
    if f(x)
    {
        // Multiple lines of work
    }
    if g(x)
        { doSomething(x); }
    if bar => break;
}
```

## Parameter placements

Function parameters should all be one one line or each on a separate line. If on separate lines, the left parenthesis is on the line with the function and the first parameter goes on the line after. The close parenthesis goes on the line with the last parameter. The exception is the self parameter which stays on the line with the function unless it doesn't fit. When parameters are on separate lines the return type is on its own line starting with `->`.

## Code Width

Stay within 80 characters most of the time, never go beyond 100. This is not because of what fits on screen, but rather, what humans can easily read and which parts of text humans focus on.

## Other

* Prefer named constructors to a method that makes an instance (for example making a child node, copy or converted value)
* In constructors, always use self to access fields and properties, in methods, use only when necessary. (There should be compiler warnings for this).
* Use spaces instead of tabs (need compiler warning for this).

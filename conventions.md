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
| Constants           | `PascalCase`**                            |
| Global variables    | `ALL_CAPS`                              |
\* usually single uppercase letter: `T`<br>
\*\* Acronyms are capitalized and run together: `ScreenDPIToPrint`

## Curly Brace Placement

Curly braces should almost always be on their own line, never K&R style with the open brace on the same line. However, for control flow statements with a single statement, the braces and statement can appear on a single line indented. If the single statement is short it may appear on the same line as the control flow. This should generally be for single keyword statements or returns of a single variable (not a more complex expression).

```adamant
for x in 1..10
{
    if f(x)
        { doSomething(x); }
    if bar { break; }
}
```

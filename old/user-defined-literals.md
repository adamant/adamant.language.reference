# User Defined Literals

Like C++, Adamant has user defined literals. User defined literals are values surrounded with single quotes such as `'c'`, `'♠'`, `'2018-09-28'` or `'c29a3471-ea8d-40e3-bb2b-ef563687f'`. All the escape sequences for strings are valid in a user defined literal. However, string interpolation is not supported in a user defined literal. User defined literals are converted to a string value which is passed to a special constructor evaluated at compile time to create a constant value.

## Literal Constructor

User defined literals are constructed with a special constructor. The resulting object must be read only and has the static lifetime. This constructor must be an implicit meta function and has the special name `''`. If it is a `struct` constructor, it must be an `init` function.

```adamant
public struct Example
{
    public let value: string;

    public implicit init ''[value: string]
    {
        self.value = value;
    }
}
```

## Type Determination

Which type a user defined literal is for is determined by type inference. This means one can declare the type of a variable to force what literal will be constructed. However, this process can be further limited by filtering.

### Requiring match

To filter which literal constructors a user defined literal might match, add preconditions to the constructors. These should not filter to only the allowed values, but to a set of values that a developer might reasonably think could match.

```adamant
public struct Date
{
    public implicit init ''[value: string]
        requires value.matches(#"\d+-\d+-\d+")
    {
        // construct date
    }
}

# Extensions

A class can be extended with additional traits and methods using an extension. To use an extension, you must use the namespace it is declared in.

```adamant
public extend class Class <: Trait
{
}
```

Note: extensions have access to protected members of their class

## Notes

Swift protocols mix required methods which "dispatch dynamically" with extensions which "dispatch statically". That seems really confusing. This is connected to the idea in Rust that you don't arbitrarily extend structs, but rather, you implement traits for them. That provides structure to the idea of dispatch. You are adding that functionality only when you can see it has having that type.

Another way to think of this is the different between interface methods in C# and extension methods.  That makes it seem like the two should be clearly different syntaxes. Luckily, we have a ready made syntax for this. Consider the following function outside any class.

```adamant
public fn my_method(self: Example) -> int
{
    return self.field * 2;
}
```

That clearly looks like an extension that is outside of the class and would be statically dispatched. It could be invoked using regular function syntax by qualifying it with the namespace. This even allows extension properties and operators! (Though operators should perhaps just be static functions instead)

```adamant
public fn get property(self: Example) -> int
{
    return self.field * 2;
}

public fn operator +(self: int, x: Example) -> int
{
    // ...
}
```

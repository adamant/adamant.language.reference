## Struct Initializers

Structs are allocated on the stack instead of the heap. To reflect this, structs are not constructed using the `new` keyword. Instead, they are initialized using initializer functions. Initializer functions are similar to constructors in that they enforce definite assignment. However, they are called more like associated functions.

An initializer may be named or unnamed. They are declared using the `init` keyword.

```adamant
public move struct Example
{
    public init()
    {
    }

    public init named()
    {
    }
}

let x = Example();
let y = Example.named();
```

Like constructors, initializers have an implicit self parameter. However, this parameter is passed `ref var self` so no copying or initialization is necessary to invoke the initializer.

**TODO:** This is an example where the distinction between `var` and `mut` may make sense on struct types. It might make sense to have the parameter type be `ref mut self` so you can mutate self, but not assign it a new value. However, C# allows assignment to `this` in struct constructors.

### Default Initializers

A struct without any initializers will have a default constructor generated for it.

### Field Initialization Shorthand

Just like constructors, there is a shorthand for initializing fields.

```adamant
public init(.field)
{
}
```

### Definite Assignment

Like constructors, definite assignment of fields is enforced. Since a struct never has a base class. The transition point to fully initialized is always implicit.

### Copy Initializers

**TODO:** document how copy works on struct types. Or should struct copy be required to be bitwise?

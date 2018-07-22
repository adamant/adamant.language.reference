# Variable Bindings

Almost all programs require variables. The standard form of variable binding is the `let` statement.

    public Main() -> void
    {
        let x = 5;
    }

In the rest of this section we will leave off the declaration of the main function. It is possible to have global variables declared outside of any function, but the rules for those are more complicated.

## Type Annotations

Adamant is a statically typed language, meaning that every expression and variable has a type determined at compile time. Our first example doesn't indicate the type of `x` though. That is because Adamant has type inference. It is able to figure out the type of something without you typing it out.

If you want or need to spell out the type of a variable you can do so by placing it after a colon.

    let x: int = 5;

You can read this as let x be an int with the value 5. The `int` type is one of the many [primitive types](primitive-types.md) in Adamant. It is a 32 bit signed integer. There are other types for the various standard integer sizes.

In future examples, we many annotate the type of a variable that Adamant can infer using a comment to make sure the type is clear to you. That would look like:

    let x = 5; // x: int

## Mutability

By default all values and bindings are *immutable*. Meaning they can't be changed. Since integers are inherently immutable (the number 2 is always the number 2), we'll use the `Point` class in our examples. This code won't compile:

    let p = new Point(4, 5);
    p.X = 6;

It will give an error on the second line saying that it is immutable and can't be assigned to. If we want to be able to mutate it, we must declare the value as mutable.

    let p = mut new Point(4, 5); // p: mut Point
    p.X = 6;

Now the point `p` is mutable and can be changed. But the variable `p` is still immutably bound to that particular point and can't be assigned a different point.

    let p = mut new Point(4, 5); // p: mut Point
    p.X = 6;
    p = new Point(10, 10); // compile error: can't assign to immutable binding

If we want to be able to assign `p` a different point to reference we need the variable binding to be mutable. We declare a mutable binding using the `var` keyword.

    var p = mut new Point(4, 5)
    p.X = 6;
    p = new Point(10, 10);

Now, we can both modify which object is referenced by `p` and the value of that object. Notice that the two are independent. We can declare a variable to an immutable object.

    var p = new Point(4, 5);
    p = new Point(10, 10);
    p.X = 12; // compile error: can't assign into immutable object

For some types, the value is inherently immutable and we need to use `var` to be able to change it at all. A common example of this is `int`s.

    var x = 5; // x: int
    x = 7;
    // The next line does not compile. compile error: `int` is an immutable type and can't be declared mutable (`mut`).
    let y: mut = 6; // y: mut int

There are many reasons things are immutable in Adamant by default. One of the primary reasons is safety. By making things immutable by default the compiler can check if you accidentally mutate something you didn't intend to.

In general, you should prefer immutable data and avoid mutation when possible. Use `let` rather than `var` and don't declare things `mut` when possible. However, there are times when mutation is what is needed.

## Definite Assignment

Another way variables in Adamant are different than in many other languages is that they must be assigned a value before you can use them.

    let x: int;
    let y = x + 5; // compile error: can't use x before it is assigned a value

The compiler will check that all variables have definitely been assigned along all code paths before they are used.

## Rebinding

A `let` binding can be redeclared in order to bind a name to a different value.

    let x = 6; // x: int
    let y = x + 6; // x is an int here
    let x = "Hello"; // x: string
    let s = x + " World"; // x is a string here

Note this can only be done with `let` bindings, not with `var`. This is to avoid confusion between assigning a new value to a variable and redeclaring it.

## Scopes and Shadowing

Variables declared in a scope are only usable inside that scope after they are declared.

    let x = 65;
    {
        let y = 3;
        let z = x + y; // both `x` and `y` are in scope
    }
    let p = x; // `x` is in scope
    let q = x + y; // compile error: `y` can't be accessed because it is out of scope

When a variable hides another variable in an outer scope with the same name, that is called shadowing. Most languages allow shadowing everywhere. Adamant does not allow shadowing within functions.

    let x = 65;
    {
        let x = 2; // Not allowed, this `x` shadows the other `x`
    }
    let y = x; // `y` will be 65

Shadowing is allowed between a variable and a class member or declaration outside the class. However, Adamant follows the tradition of C# in not allowing shadowing within the parameters or variables of a function. This avoids programmer confusion and prevents bugs while almost never causing inconvenience.

## Patterns?

A section about destructuring patterns?

# Reference Types

There are two kinds of types in Adamant, reference types and value types. Reference types are so named because they are passed by reference. Value types are, as one would expect, passed by value. In this section, we'll explain reference types because most of the types you create in Adamant will be reference types. Later we'll explain value types.

In languages like C#, all reference types are stored on the heap with pointers to them on the stack or in other objects. All value types, on the other had, are stored direct on the stack or in the object declaring them. That is not the case in Adamant. The ownership system gives the Adamant compiler a lot more information about how memory is used in your program. As a result, it is able to optimize many uses of reference types to store them directly on the stack or in the referencing object. Just because a reference type has to be placed on the heap in one usage, doesn't mean it is on the heap everywhere it is used either. This is an important feature of Adamant as it allows you to worry about what your app does rather than where objects are stored while being confident the best storage choice is being made. It also means that a minor change necessitating a change in storage is a minor change rather than a big project of changing all uses of the value.

Any type declared as a class in Adamant is a reference type. Let's consider a simple reference type for points.

    public mut class Point
    {
        public var x: int;
        public var y: int;

        public new(x: int, y: int)
        {
            self.x = x;
            self.y = y;
        }
    }

If you've read the section on [variable bindings](variable-bindings.md) and are familiar with other object oriented languages, this should be pretty clear to you. The declaration that looks like a function except with the `new` keyword instead of a name is how a constructor is declared in Adamant. It lets us create a point given values for `x` and `y`. You can see the `x` and `y` values are mutable. The `int` type is a value type, but don't worry about that for now. They behave like you would expect.

## Reference Ownership

When a new object is created, the first variable it is placed in has *ownership* of the reference.

```adamant
let a = new Point(4, 3);
console.WriteLine("a = (\(a.x), \(a.y))");
```

Here the `a` variable owns the reference to the `Point` object. When `a` goes out of scope, the point will be automatically released.

## Borrowing

When a reference is passed to another variable or function, it is *borrowed*. A borrowed reference is not responsible for releasing the value it references.

```adamant
public distance_from_origin(p: Point) -> float
{
    return sqrt(p.x*p.x + p.y*p.y);
}

// In Main
let a = new Point(4, 3);
console.write_line("a = (\(a.x), \(a.y))");
let distance = distance_from_origin(a);
console.WriteLine("distance from origin \(distance)");
```

Here the `distance_from_origin` borrows a reference to point referenced by `a`. By default, borrowed references are immutable. They can't be used to mutate the object being referenced. If we tried to insert `p.x *= 2` as the first line of `distance_from_origin` we would get a compile error saying that `p` can't be mutated.

## Mutable Borrowing

It is also possible to borrow a reference to a mutable object. To do this use the `mut` keyword when passing the value. If taking a mutable borrow with a function parameter, simply declare the parameter as `mut`.

```adamant
let a = mut new Point(4, 3);
{
    let b = mut a; // b: mut Point
    b.y = 6;
}
console.WriteLine("a = (\(a.x), \(a.y))");
```

This will print "a = (4, 6)" because the point object is mutated through the `b` reference to it. Mutable borrowing is different from immutable borrowing. As an example, consider:

```adamant
let a = mut new Point(4, 3);
let b = mut a; // b: mut Point
console.WriteLine("a = (\(a.x), \(a.y))"); // error
b.y = 6; // mutate the point
```

In this example, you'd get a compile error on the third line saying `a` could not be immutably borrowed because it was already mutably borrowed. It turns out there are rules to borrowing, but before we get to those, let's talk about handing off ownership.

## Moving References

We've seen how references can be borrowed both immutably and mutably. However, sometimes one needs to actually transfer ownership of a reference to change who is responsible for releasing the object. To do this, use the special lifetime `$owned` and the `move` keyword. Lifetimes are explained in detail later. Every reference has a lifetime, and the lifetime of all references that own their value is the special lifetime `$owned`. In all the examples above, the lifetime has been inferred by the compiler, so it wasn't necessary to type it.

Imagine we need a function that will use up an object some how. Say it will read the rest of and close a file. Then it will want to take responsibility for releasing that object. Of course, any such example for points will be a little contrived, but here goes.

```adamant
public consume(console: mut Console, p: Point$owned) -> void
{
    console.WriteLine("consumed (\(p.x), \(p.y))");
}

// In Main
let a = new Point(4, 5);
consume(mut console, move a);
```

Here we declare the `consume` function as taking ownership of the point it is passed by declaring the lifetime as `$owned`. When we call the consume function we must explicitly indicate that we are passing off ownership of the point using `move`. This makes the intent of the code very clear to anyone who might read it in the future.

Once a reference has been consumed it can no longer be used by its original owner.

```adamant
let a = new Point(4, 5);
consume(mut console, move a);
consume(mut console, move a); // compile error
```

The second call to consume will not compile, giving an error that the reference in `a` has been moved out of `a`.

When passing ownership, mutability works a little differently. When taking ownership of a reference, we can change its mutability. This is safe because we are the only ones with a reference to it and we own it, it is ours now to do whatever we want with. Unlike borrowing, where mutability is declared when passing a value, mutability is declared where ownership is received.

```adamant
public transform(p:  mut Point$owned) -> Point$owned
{
    p.x *= 2;
    p.y *= 3;
    return move p;
}

// In Main
let a = new Point(4, 5);
let b = mut transform(move a);
b.x -= 1;
```

Notice that `a` is immutable and when calling `transform` we move the reference without indicating it is mutable. However, the `transform` makes the point mutable when it takes ownership of it. When `transform` returns the reference, it does so as immutable (Note: emit compiler warning for `mut` placed in returned ownership). However, we want to mutate the result so we use `mut` in front of the call to `transform`. Any time something is returning ownership, the mutability is determined by the destination. Using `mut` on the result is just shorthand.

```adamant
// This code
let a = mut new Point(4, 5);
let b = mut move a;
let c = mut transform(move b);

// Is Equivalent to
let a: mut = new Point(4, 5)
let b: mut = move a;
let c: mut = transform(move b);
```

## Borrowing Rules

Here are the rules for ownership and borrowing in Adamant.

**TODO:** These rules may be incorrect and need revised

1. Only one reference can have ownership of an object at a time.
2. You must have ownership to transfer ownership
3. No borrows can exist when transferring ownership.
4. Any borrow must last for a lifetime less than that of the reference being borrowed from.
5. You may have one or the other of the two kinds of borrows, but not both at the same time.
  * One or more immutable borrows of an object
  * Exactly one mutable borrow of an object

These rules are enforced by the compiler. The portion of the compiler responsible for this is often called, the *borrow checker*. You may notice these rules match those needed for safe current access to data. That is how Adamant provides data race safety through its ownership system.

## A Second Look at Borrows

Let's consider again an example from earlier.

```adamant
let a = mut new Point(4, 3);
let b = mut a; // b: mut Point
console.write_line("a = (\(a.x), \(a.y))"); // compile error
b.y = 6; // mutate the point
```

Now that we know the rules of borrowing, we can see that the second line makes a mutable borrow of the point in `b`. On the third line, the borrow `b` is still alive because it will be used on the fourth line. But the call to `WriteLine` needs to borrow `a` immutably in order to read the values of `X` and `Y`. This isn't allowed at the same time, so we get a compiler error.

The code that did compile was:

```adamant
let a = mut new Point(4, 3);
{
    let b = mut a; // b: mut Point
    b.Y = 6;
}
console.write_line("a = (\(a.x), \(a.y))");
```

Here the lifetime of `b` clearly ends before the call to `write_line` since `b` goes out of scope at the end of the block surrounded with curly braces.

## Issues Ownership and Borrowing Prevent

Why have these rules around ownership and borrowing and add the complexity of move? Well, these rules provide safety for concurrency and other issues including iterator invalidation and use after free.

### Iterator Invalidation

**TODO:** write section

### Use After Free

References must not last longer than the resource they refer to. Adamant will check the uses of references to ensure that this is true. If this rule were not enforced, we could use an invalid reference. In manual memory management this is known as 'use after free'.

```adamant
let x: ref int;
{
    let y = 85;
    x = ref y;
}

console.write_line("\(x)");
```

When we try to compile this, we get an error because `y` does not live long enough for `x` to reference it.

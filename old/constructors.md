# Constructors

Constructors are declared as class members with the new keyword:

```adamant
public class Example
{
    public new()
    {
    }
}
```

and called with the new keyword.

```adamant
let x = new Example();
```

Constructors can also be named:

```adamant
public class Example
{
    public new named()
    {
    }
}

let x = new Example.named();
```

Constructors implicitly have a parameter of `mut self`. Notice this is not owned, because the caller will receive ownership, and the constructor is not free to pass ownership off.

Constructors can optionally be given a return type. This type must be a base type or must have at least one type parameter specified.

```adamant
public class Example: Base_Example
{
    public new() -> Base_Example
    {
    }
}
```

It is illegal to simply repeat the class name or to use a subtype or unrelated type.

### Open Issues

* If some type parameters are specified, what does that do to the type parameters when constructing?
* Can the constructor method be generic so it can control type parameters?
* How are type parameters passed when calling a constructor given that they may be changed/reduced it seems they belong with the name not the type?
* How can one combine specifying type parameters with returning a base type?

Idea:

* Perhaps it is like the constructor is a static method on a non-generic version of the class that has all the same type parameters by default

## Calling Other Constructors

A constructor must either call a base class constructor or another constructor on the class. Base class constructors are called by `base(arguments...)` or `base.Name(arguments...)` for named constructors. Other constructors on the class are called similarly with `self(arguments...)` or `self.Name(arguments...)`. It is an error for self constructor calls to form a cycle.

**TODO:** the syntax for named constructor calling here is somewhat ambiguous and it may not be clear that a constructor is being called. Perhaps a better syntax would be `base.new()` and `base.new.Name()`

## Definite Assignment

Any field initializers are run before the body of the constructor of the class.

When calling a base class constructor, all fields must be initialized before calling the base class constructor. The compiler enforces definite assignment on the fields and a field can't be used before being initialized. Up through the call to the base class constructor, the self parameter can't be used except to initialize fields (i.e. self can't be passed to a function, and methods can't be called on self). Any owned fields that have not been initialized before the base class constructor call will be automatically initialized with `none` if they are optional.

**TODO:** perhaps there is a special constructor the compiler calls for fields that are not initialized so people could define other types that auto init with a certain constructor.

When calling a self constructor, no fields can be initialized before the call. They would simply be overwritten by the other constructor. However, other statements can occur before the self constructor call for any needed computation.

**TODO:** For classes without a base class, we may need a clear way to delineate the separation between these parts of the constructor.

After the call to the self or base constructor, all fields of the instance have been initialized. This includes any fields of the subclasses. After this call, the self reference may be used and any methods on may be called on self.

### Rationale

This construction sequence is similar to Swift and ensures that all fields are initialized before any use of self. This means we do not need to zero init memory and there is no chance of using uninitialized memory.

## Field Initialization Shorthand

Fields can be directly initialized from constructor arguments. This is done by prefixing the parameter name with `.` and omitting the type. The type is determined by the type of the field. This can also be used with property setters.

```adamant
    public new(.field)
    {
    }
```

## Exceptions

Exceptions in constructors can be complex and easily lead to issues. For this reason constructors are implicitly `throws void` instead of the default inferred throws. However, constructors can throw exceptions if they are declared to throw them.

If an exception is thrown in a constructor, destructors will not be run on the instance except as part of stack unwinding as described below. Exceptions thrown in or before a base or self constructor call simply unwind the stack, deleting any initialized field values. Exceptions thrown after the completion of a base or self constructor call invoke the *base* classes destructor on this instance at the point of unwinding past the base class call (since it is now a fully constructed instance of the base class).

Note that the current classes destructor is never called so it is imperative that any resources be properly released by the constructor.

If you catch an exception from a self or base constructor call. You must re-throw it, because the current instance is not in a valid state.

## Partially Initialized References?

The definite assignment rules lead to some real problems. For example, imagine a tree node class with left and right child node fields and a parent node reference. We must make the parent reference optional and mutable, and initialize it after the child. Otherwise, we have a problem initializing the tree even from in the parent constructor. The parent node needs to construct its two child nodes so that it can initialize its fields. However, to construct them it must provide a reference to itself. Yet, it is not completely initialized yet, so this is not safe. It might be possible for the borrow checker to provide a way out of this. There could be a special kind of reference which does not allow for any methods or fields to be accessed but can only be stored with the promise that it would be valid in the future. However, the borrow checker rules for this would probably be very complicated.

## Constructor Inheritance

Swift uses convenience constructors to allow constructor inheritance. I might be able to do something similar by making designated constructors take self as an argument while convenience constructors don't, but must make a new object. Of course, I need to figure out how to make it explicit that you must return the result of calling new on your own type.

## Copy Constructors

Copy constructors are special constructors used to make a copy of the class. They have the unique property of being virtual on the type of object being copied, even though that it passed as a parameter. They are declared with the special `copy` constructor name and take an argument of type `Self` or `mut Self`.

    public new copy(mut self, value: Self)
    {
        // copy fields
    }

Construction proceeds through the inheritance hierarchy just as with normal constructors, starting with the most concrete type of the object being copied. Any field that has an implicit copy constructor and isn't explicitly assigned in the constructor is automatically copied as part of the compiler generated copy.

Copy constructors are called with the special syntax

    let x = new copy(y);

Note that we do not have to specify the type being copied. It is the dynamic type of the parameter.

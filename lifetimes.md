# Lifetimes

In discussing the ownership system so far we have glossed over one very important aspect. That is lifetimes. In order to ensure the safety of references the compiler must enforce that no references live longer than the value they reference. The way that is done is by considering the lifetime of all references and values.

## Scopes

Before we get to lifetimes, it is necessary to review scopes as they are the scaffolding that lifetimes fit into. Every variable has a lexically defined scope in which it can be used.

    public Main(console: mut Console) -> void     // -+ `console` comes into scope
    {                                             //  |
        console.WriteLine("Beginning Execution"); //  |
        let x = 42;                               // -+ `x` comes into scope
        console.WriteLine("The answer is {x}");   //  |
        console.WriteLine("Ending Execution");    //  |
    }                                             // -+ `x` then `console` go out of scope

A variable comes into scope when it is declared and goes out of scope at the end of the block it is declared in. In most languages, variables are defined to go out of scope in the opposite order they came into scope. That matters to the order of destructor execution. A variable's scope is the span of code it is valid to reference the variable in and in most languages the span of time the value the variable holds is alive.

## Lifetimes

In Adamant, values and references have lifetimes. Lifetimes are grounded in the scopes of the variables that hold them, but are more flexible.

    public Main(console: mut Console) -> void     // -+ `console` reference lifetime begins
    {                                             //  |
        console.WriteLine("Beginning Execution"); //  |
        let x = 42;                               // -+ `x` value lifetime begins
        console.WriteLine("The answer is {x}");   // -+ `x` value lifetime ends
        console.WriteLine("Ending Execution");    // -+ `console` reference lifetime ends
    }

The lifetime of something is that span of code in which it might actually be used. We say might because in situations of conditionals and loops, a value has the potential to be used but may not be used depending on the values at runtime. A value or reference's lifetime begins at the moment it comes into existence and ends at the last possible moment it might be used.

Destructor execution in Adamant occurs sometime between the end of the lifetime of the value and the end of the scope of the variable holding the value. Typically, destructors are run when the lifetime ends, but the compiler is free to optimize this by reordering destruction relative to unrelated operations. Of course, there are some situations when lifetimes are interdependent, in which case the destructors must be executed in an order the respects the lifetime dependency.

    let x = 45;
    let y = ref x;
    console.WriteLine("x + y = {x+y}");

Here, `y` is a reference to `x` so the lifetime of the reference must end before the lifetime of the value it references. Consequently, the reference is destroyed before the value it references.

## Lifetimes in Functions

Most of the time, lifetimes are implicit and the Adamant compiler automatically figures out the correct lifetimes. However, we can also explicitly declare the lifetime relationships of the parameters (and return) of a function.

    // implicit
    public Foo(a: Point) -> void
    {
    }

    // explicit
    public Foo<~x>(a: ~x Point) -> void
    {
    }

Lifetimes are marked with a tilde '~'. The `~x` is read as 'the lifetime x'. Every reference and value has a a lifetime, but the compiler lets you omit them in many cases. To specify lifetimes one can use lifetime parameters or refer to the lifetime of another variable.

    public Bar<~x>(a: ~x Point, b: ~x Point) -> void
    {
    }

    // equivalently
    public Bar(a: Point, b: ~a Point) -> void
    {
    }

In the first declaration, we create an explicit lifetime variable `~x`. In the second, we simply refer back to the lifetime of the first parameter to describe the lifetime of the second by using `~a`, the lifetime of `a`.

When declaring the lifetimes of mutable references, the lifetime goes before the `mut`.

    public Baz<~x>(a: ~x mut Point) -> void
    {
    }

## Reference Lifetimes

Most of the lifetimes we need to specify in Adamant are for references. Either the references of reference types or the references to variables. Each reference has some lifetime that is less than the lifetime of the value it references.

    public OneOf(x: ref int, y: ref~x int) -> ref~x int
    {
        // randomly returns x or y
    }

Here we declare that the lifetime of the reference in `y` is the lifetime of the reference in `x` and the return type is a reference with the same lifetime.

## `own` Lifetime

There a special lifetime `~own` that indicates a reference controls the lifetime of the value it references, and is not bound by any other lifetime. Unless the reference is moved to a different variable, the lifetime of the reference will end before the scope of the variable it is in. In essence, an `~own` reference owns the value it references and is owned by the variable it is in. If something has a lifetime of `~own` we typically say it is owned and conflate the reference and value.

Objects of reference types are owned by their original reference. The reference returned by `new`. That reference is owned by the variable it is assigned into. By that means, the actual lifetime of the value will be determined. Effectively, when the root of the chain of ownership goes out of scope, a value with be destructed. We can extend the lifetime of a value by moving the ownership of the reference that owns it.

    public MakePointOnXEqualsY(v: int) -> ~own Point
    {
        return new Point(v, v);
    }

Here we extend the lifetime of the point created in the function by returning ownership of the reference to it. As we saw in the section on move semantics. If a variable takes ownership then we must move the ownership from its source. Consider again the consume function.

    public Consume(console: mut Console, p: ~own Point) -> void
    {
        console.WriteLine("consumed ({p.X}, {p.Y})");
    }

    // In Main
    let a = new Point(4, 5);
    Consume(mut console, move a);

When calling consume we must move the reference into the function, because it declares that it takes ownership of the reference. Since the reference has ownership of the value, the point object will be destructed before the end of the function.

## Lifetime of Borrow References

When borrowing, the borrow reference has a lifetime that is shorter than the reference we are borrowing from. That reference may be the reference that owns the value or may itself be a borrow that is shorter than the owning lifetime.

## Lifetime of Value Types

Value types are typically owned by the variable that holds them. A type with move semantics carries ownership along with it as it moves from variable to variable. A type with copy semantics produces new copies of the value each of which are owned by the variable that holds them.

When reference are taken to variable of value types, their lifetimes operate exactly as borrows. The reference lifetime is constrained to be less than the lifetime of the value being borrowed from.

There are unusual cases in which the lifetime of a value type may be something different than `~own`. This can occur when a value type is actually acting as a reference to a value. For example, the `string` method `Trim()` which removes whitespace from the beginning and end of a string is declared to return a string with a limited lifetime.

    public Trim(self) -> ~self string
    {
        // ...
    }

So the resulting string has a lifetime bound by the string we are trimming from. That is because the result is really a reference to the portion of the first string excluding the whitespace.

## Lifetimes of Variables Can Change

The ownership on a local variable is implicit, and can actually change when a new value is assigned to it.

    let p = new Point(4, 6); // `p` owns Point(4, 6)
    var q = new Point(8, 9); // `q` owns Point(8, 9)
    q = p; // Point(8, 9)'s lifetime ends, `q` borrows Point(4, 6) with some lifetime < ~p

## Lifetime Parameters to Classes and Structs

Classes and structs holding references typically own them so that contained values will be destructed when the object it. However, they can also take lifetime parameters so that they can hold borrowed references.

    public struct IntRef<~v>
    {
        public let Value: ref~v int;

        public new(value: ref~v int)
        {
            Value = value;
        }
    }

Additionally, structs can use the special lifetime `~self` to refer to the lifetime they are held with. The `string` struct uses a lifetime of `~self` to reference the bytes of the string so that some strings can own their data while others borrow it.

When types have generic parameters we can capture an associated lifetime for those parameters if needed.

    public class List<~t T>
    {
        // TODO not sure how to make use of this.
        // use of `~t`
    }

**TODO:**

A better approach is probably to allow referencing the lifetime of a type parameter.

    public class List<T>
    {
        // TODO not sure how to make use of this.
        // use of `~T`, the lifetime associated with type T
    }

## The `~static` Lifetime

There is another special lifetime, `~static` which indicates that a reference may live the length of the entire program and the value it references will never be deleted. All constants and global variables have lifetimes of `~static`.

A static lifetime may be passed where ownership is expected if the value referenced is inherently immutable. If the value is not inherently immutable this is not allowed because it would allow the recovery of mutability of constant and static values which would violate the safety of the language. This is needed to allow a function like:

    public DisplayValue(x: int) -> ~own string
    {
        return int.TryParse(x) ?? "(none)";
    }

Here, the lifetime of the string constant is `~static` and the reference returned by `TryParse()` is owned, but both are compatible with the return type of `~own string`.

Note: This will require something like a drop flag (or tagged pointer) to track whether the owned reference should be deleted. A better alternative may be a check when deleting to see if the reference is into the static data area. This check could be skipped for types that are not inherently immutable.

## Lifetime Elision

Every reference and value has a lifetime, and it is required for the types of function to be explicit. Yet, many functions do not have the lifetimes of their parameters or return value explicitly stated. However, for convenience, certain inference rules apply for lifetimes in functions. These are simply unambiguous rules that for the *lifetime elision* rules. They apply whenever a lifetime is elided, i.e. omitted from a function signature. The lifetime elision rules are defined in terns of *input lifetimes* and *output lifetimes*. An input lifetime is any lifetime associated with a parameter of the function. An output lifetime is any lifetime associated with the return type of a function.

  * Each elided lifetime in a function’s arguments becomes a distinct lifetime parameter.
  * If there is exactly one input lifetime, elided or not, that lifetime is assigned to all elided lifetimes in the return values of that function.
  * If there are multiple input lifetimes, but one of them is `self`, `mut self`, `ref self` or `ref mut self` the lifetime of self is assigned to all elided output lifetimes.

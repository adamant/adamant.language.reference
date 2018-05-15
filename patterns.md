# Patterns

Match patterns do two things, they describe the shape of data that should match, and they destructure that data into variables to access specfic portions.

## Uses

* `match` expression arms
* `if let` expressions
* `while let` expressions
* `for` expressions
* `let` and `var` statements
* function parameters (should this be supported?)

## Refutability

Some pattern matches can't fail, others can. Whether a pattern is refutable or not determines where it can be used.

## Pattern Syntax

### Literals

```adamant
let x = 1;

match x
{
    1 => console.write_line("one"),
    2 => console.write_line("two"),
    3 => console.write_line("three"),
    _ => console.write_line("anything"),
}
```

`string` and `code_point` literals also work.

### Variable Names

A variable name declares a variable and always matches.

### Matching Multiple Patterns

```adamant
let x = 1;

match x
{
    1 | 2 => console.write_line("one or two"),
    3 => console.write_line("three"),
    _ => console.write_line("anything"),
}
```

Note that alternatives can contain the same variable name as long as the type matches in each alternative.

### Ranges

Ranges work exactly as they do in other places

```adamant
let x = 5;

match x {
    1..5 => console.write_line("one through five"), // TODO but .. isn't inclusive?
    _ => console.write_line("something else"),
}
```

How can this be overloaded to match other types?

### Tuples

```adamant
let #(x, y) = #(1, 2)
```

### Matching Based on Type

```adamant
let x = new Foo();

match x
{
    f: Foo => console.write_line("Foo"),
    _ => console.write_line("not Foo"),
}
```

Any class type can be matched. If the type is not visible outside the package, then the match can be exhaustive because no subclasses can be added without rebuilding the package. This can also be achieved by marking the class "closed?"

### Optional Types

```adamant
if let x? = value
{
    // x is value
}
```

If the `?` operator can be overloaded, then it should be possible to overload this to match for other types besides optional.

### Ignoring Values

```adamant
foo(console: Console, _: int, y: int) -> void
{
    console.write_line("This code only uses the y parameter: \(y)");
}
```

Can be used to ignore sub-parts.

### Unexpected Values

There should be a syntax similar to `_` that serves as a catch all for match expressions, but generates a warning if the compiler detects something will match it. This would be useful for libraries that contain open types where you must have a `_` but don't want to accidentally match added types. But is this different than `_ => NOT_IMPLEMENTED!()"?

Should you really be required to have a default case for matches on open types? If the library is changed, you should deal with it.

### Unused Variables

Use `_x` to avoid the warning that variable `x` is unused. As with Rust, this has the affect of binding the value to the variable which has lifetime effects, whereas just `_` does not bind.

### Conditionals for Match Guards

```adamant
let num: int? = 4;

match num {
    x? if x < 5 => console.write_line("less than five: \(x)"),
    x => console.write_line(x),
    none => void,
}
```

### Object Properties

```adamant
class Point
{
    public let x: int;
    public let y: int;

    public new(.x, .y) { }
}

main() -> void
{
    let p = new Point(0, 7);

    let Point { x, y } = p;
    assert(0 == x);
    assert(7 == y);

    // TODO is this the correct syntax?
    let Point { a=.x, b=.y } = p;
    assert(0 == a);
    assert(7 == b);
}
```

### Arrays/Lists?

```adamant
let [x, y, ...] = list;
```

How do you expand this to work on other types? A special kind of match method.

```adamant
public class Foo
{
    match []() // creates array match method?
    {
    }
}
```

### Match Methods

Classes can declare how they are matched with match methods. These are like Scala unapply functions.

```adamant
class Foo
{
    match(.x, .y) => true; // match the x and y properties
    match() => #(int, int)? // Form allowing more sophisticated logic
    {
        return #(x, y);
    }
    match named(.x, .y); // matches can be named
    new match (.x, .y) // declare both a constructor and a match method at once
    {
    }
}
```

These are then matched as

```adamant
let v = new Foo();

match v
{
    T(x,y) => ...,
    T.name(x, y) => ...,
    .name(x, y) => ..., // type assumed to be that of `v`
}
```

Should classes that are tuple like be supported as a shortcut for matching?

```adamant
public class T(a: int, b: int) // declares fields `a` and `b`, constructor and match method all at once
{
}
```

### Regular Expressions

Is there a way to use a regular expression to match a string?

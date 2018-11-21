# Anonymous Functions

Anonymous functions allow the creation of unnamed functions inside another function.

```adamant
let add_two = fn(x) => x + 2; // addTwo: (int) -> int
```

The parameter types can be inferred. However, sometimes it is necessary or clearer to directly state them.

```adamant
let say_hello = fn(name: string) => "Hello " + name + ".";
```

The above examples are simple functions that only require a single expression to compute. However, an anonymous function can have a complete function body by using a block.

```adamant
let repeat_n = fn(n, func)
    {
        foreach _ in 0..<n
        {
            func();
        }
    };
```

## Closures

Anonymous functions are closures, thus they have access to the variable values in the surrounding function.

```adamant
make_adder(n: int) -> (int) -> int
{
    return fn x => x + n;
}
```

**TODO:** explain the complexities of closures in a language with a borrow checker

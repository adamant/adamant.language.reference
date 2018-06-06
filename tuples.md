# Tuples

A tuple is an ordered list of values that can be of different types.

```adamant
let t = #(1, "something");
```

A tuple constructor follows the pattern of other composite value constructors of using the hash sign followed by some form of brackets. Here is the same declaration with the types annotated.

```adamant
let t: #(int, string) = #(1, "something");
```

The types of tuples look like the tuple with types instead of values. Tuples are reference types like most objects in Adamant.

## Destructing With `let`

You can access the individual fields of a tuple using a destructuring let.

```adamant
let #(x, y, z) = #(1, 2, 3);
console.WriteLine("x = {x}");
```

## Indexing Tuples

You can access the fields of a tuple by index. Tuple indexes are one based because we normally refer to the "first" item in the tuple. Since integers would not normally be valid identifiers, they must be escaped with the backslash.

```adamant
let t = #(1, 2, 3);

let x = t.\1;
let y = t.\2;
let z = t.\3;
```

## The `Tuple<...>` Type

**TODO:** Given the above syntax is just composite constructor syntax, it should be possible to say `Tuple<int, string>` and `new Tuple(4, "hello")`. In fact, tuples may be fully implemented in the standard library.

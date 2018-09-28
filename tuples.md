# Tuples

The `Tuple[T...]` type is declared in the standard library. A tuple is an ordered list of values that can be of different types. Tuples can be constructed using the [tuple initializer](composite-initializers.md) syntax.

```adamant
let t = #(1, "something"); // t: Tuple[int, string]
```

Tuples are reference types like most objects in Adamant.

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

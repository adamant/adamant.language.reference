## Tuple Types

The tuple types are a family of generic types. Like the simple types, they are identified by the keyword "`Tuple`". A tuple is an ordered list of values that can be of different types.

Tuples are value types. If all the type parameters are implicitly copyable, then the tuple is implicitly copyable. If all the type parameters are implicitly *or explicitly copyable* then the tuple is explicitly copyable. If any type parameter is not copyable, the tuple is a move type.

```grammar
tuple_type
    : "Tuple" "[" type{",", 0, } "]"
    ;
```

### Tuple Size

The number of values in a tuple can be accessed using the "`count`" field which is of type "`size`".

### Tuple Initializer

Tuples can be constructed using the tuple initializer syntax.

```adamant
let t = #(1, "something"); // t: Tuple[int, string]
```

### Destructing With `let`

The individual fields of a tuple can be accessed using a destructuring let.

```adamant
let #(x, y, z) = #(1, 2, 3);
console.WriteLine("x = \(x)");
```

### Indexing Tuples

There are two ways to index into the fields of a tuple. In both ways they are accessed by a zero based index. This is consistent with arrays which are zero based and makes more sense for the "`at`" method.

Tuple values can be directly accessed as fields by escaping the index name.

```adamant
let t = #(1, 2, 3);

let x = t.\0;
let y = t.\1;
let z = t.\2;
```

Alternatively, they can be accessed using the "`at[n:size]()`" method similar to how arrays and lists are index. However, for tuples, the index must be known at compile time so the access can be type checked. So the index must be passed as a generic argument.

```adamant
let t = #(1, 2, 3);

let x = t.at[0]();
let y = t.at[1]();
let z = t.at[2]();
```

Note that the "`at`" method can't be a meta-function because it must return a reference to a runtime value.

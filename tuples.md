# Tuples

A tuple is an ordered list of values that can be of different types.

	let t = new [1, "something"];

Adamant has no primitive arrays.  Generally collections are of `List<T>` or sometimes `Array<T>`.  Instead, square brackets are used for tuples.  Though square brackets are still used for indexing into a collection.  Here is the same declaration with the types annotated.

	let t: [int, string] = new [1, "something"];

The types of tuples look like the tuple with types instead of values.  Tuples are reference types like most objects in Adamant.

## Destructing With `let`

You can access the individual fields of a tuple using a destructuring let.

	let [x, y, z] = new [1, 2, 3];
	console.WriteLine("x = {x}");

## Indexing Tuples

You can also access the fields of a tuple by index. Tuple indexes are one based because we normally refer to the first item in the tuple.

	let t = new [1, 2, 3];

	let x = t.1;
	let y = t.2;
	let z = t.3;

Note: `x.1.2` is a syntax error because it parses as `x . 1.2`. To avoid this write `x.1 .2` or `(x.1).2`. However, use of nested tuples is discouraged.

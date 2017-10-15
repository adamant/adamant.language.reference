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

You can also access the fields of a tuple by index.

	let t = new [1, 2, 3];

	let x = t.0;
	let y = t.1;
	let z = t.2;

TODO: it looks weird to access the zeroth item as `t.0`, perhaps tuple indexes should start at 1, but then that would be inconsistent with arrays.
# Copy and Move

Working from the `class`, `move class`, `copy class` framework.  It is basically like we are just saying what the default is.  Let Rx be ref classes, Mx be move classes and Cx be copy classes.

Examples
mut Rx -> ref mut Rx
mut Mx -> own mut Mx
ref Mx -> ref Mx
ref mut Rx -> ref ref mut Rx
mut ref Rx -> ref mut ref Rx

That isn't working out. What if we use the var keyword again

ref var Mx -> reference to a variable holding an immutable Mx
ref var mut Mx -> reference to a variable holding a mutable Mx

----------------
mut Rx -> * mut x
mut Mx -> mut x
ref Mx -> * const x
mut ref Mx -> 

** If you can assign into a reference to a copy class, then either they will have to be stored as references or you will need to know you are assigning a value of the exact same type. 

--------------------------------
# Final

Copy and move semantics apply to value types declared with `copy struct` and `move struct`.  For interop, structs must be used, and they must be declared `external`.

Structs declared without the `mut` keyword are immutable and all their methods must be immutable.  However, using `var` with a struct type effectively makes it mutable since the value can be overridden.  For mutable value types, there is not a separate mutability of the reference from the value.  For reference types, the mutability of the value is part of the type with `mut` and the mutability of the reference is part of the binding with `let` vs `var`.  For value types, the mutability is only part of the binding with `let` vs `var`.

Examples:

	let x: int = 6; // an immutable int value
	var y: int = 7; // a "mutable" int value (ints are immutable, but the value can be replaced)
	let p1: point = new point(4, 5); // an immutable point
	var p2: point = new point(7, 9); // a mutable point, can be replaced or have mutable methods called on it

Value types can be passed by reference using the ref keyword.

	public func(a1: ref point, a2: ref var point); // a1 is immutable, a2 is mutable (note a2 could not be declared `var`)
	
	func(ref p1, ref mut p2); // call func passing references to p1 and p2
	
Notice that references to mutable value types are `ref mut T` so they can be read left to right.

Reference types can be passed by reference allowing the modification of variables holding references.

	let a: List<int> = ...; // immutable reference, immutable list
	let b: mut List<int> = ...; // immutable reference, mutable list
	var c: List<int> = ...; // mutable reference, immutable list
	var d: mut List<int> = ...; // mutable reference, mutable list;
	
In the above, there is essentially and implicit `ref` in each type before the `mut` and `List<int>`.  When declaring parameters, this leads to strangeness around the mutability because `ref mut R` where `R` is a reference type is ambiguous whether it should mean `ref ref mut V` where `V` is the value type corresponding to `R` or `ref mut ref V`. So instead we use the `var` keyword to indicate the mutable reference.
	
	public func(a1: List<int>, a2: mut List<int>, a3: ref var List<int>, a4: ref var mut List<int>); // note a3 and a4 could not be declared `var`
	
	func(a, b, ref c, ref mut d);
	
	
	
Note that `ref T` and `ref mut T` are not allowed for reference types because they are already passed by reference and a reference to a reference doesn't make any sense unless the reference is mutable.   

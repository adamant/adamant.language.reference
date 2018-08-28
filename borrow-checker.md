# Formal Description of the Borrow Checker

This section attempts to give a more formal and precise description of how the borrow checker works. This is both for programmer's understanding and to aid in implementation. Please note that this section makes careful distinctions between similar sounding terminology.

## Lifetime of What

One of the first questions that arises in understanding and developing borrow checking is what lifetimes are and what they refer to.

### Lifetimes in Rust

In Rust, it has always been confusing what lifetime parameters mean. What lifetime do they actually refer to? I think now, that they are actually an abstract lifetime not identical to the lifetime of any value or reference. Rather, it is a constraint on the lifetimes of references and value. As one author put it:

1. The lifetime parameter of a reference type represents an upper bound for how long you can hold on to that reference.
2. The lifetime parameter of a reference type represents a lower bound for the lifetime of the things you can make the reference point to.

(source https://stackoverflow.com/a/29851211/268898)

Also note, that this definition says nothing about borrowing and the blocking of access to the primary value. Consider a function like substring:

```rust
fn substr<'a>(s: &'a str, until: u32) -> &'a str {
    "a static result"
}
```

Here, the actual reference `s` is dead after the end of the function. So `'a` can't simply be the lifetime of it. However, the returned string may be held for only a portion of the life of the string that `s` references, so the lifetime `'a` isn't simply the lifetime of the value `s` references. Rather, it is a span of time that forms a constraint relating the lifetimes of these vales.

This example also illustrates that lifetimes are independent of actual borrows. The compiler is using lifetimes to infer whether it is possible that a borrow still exists and conservatively enforcing borrow rules.

Notice though that the lifetime parameter forms a constraint on what we might call the scopes of the variables. That is how long the references and values can be in scope by being bound to some variable. These could easily be thought of as lifetimes, but it seems that officially they are not according to Rust. However, colloquially people often refer to them as such.

This all effectively makes lifetimes a property or field that a reference has that is separate from it.

### Review of References in C# and Java

Recall that in C# and Java, most types are reference types. However, the programmer doesn't think of and keep track of references as such. For example, when declaring a variable `Test t = ...;` the developer is aware that `t` is actually a reference to an object of type `Test`, but generally thinks of `t` as simply being a `Test` object. The existence of the reference fades into the background and os not the focus. This is very different from Rust where value types are the default and references must be explicitly declared for parameters and created using the `& operator.

### Lifetimes in Adamant

In Adamant, the references involved in reference types should fade into the background just as they do for C# and Java. The default in Adamant is reference types rather than value types and owning or unique references are fully built into the language rather than being a somewhat separate concept as in Rust (i.e. `Box<T>`). These combine to make it so that it doesn't seem to make sense to think of lifetimes in Adamant being associated to the object/value rather than the reference.

Given a variable declaration like `let manager: Employee = ...;` in Adamant, then the lifetime operator applied to the manager variable `|manager` should be thought of as the lifetime of the manager object. Indeed, this is what truly matters. If a reference to the manager is borrowed, that borrow needs to not outlive the manager object. It may however outlive the reference it was originally borrowed from.

The fact that lifetimes are the lifetime of the object rather than the reference is why the special lifetime `owned` is so named. It is a description of the object lifetime. Other terms like "owns" and "unique" are descriptions of the reference.

## Borrow Checking in Functions

Within the body of any function, borrow checking operates similarly to Rust. Each variable type has a lifetime component. It can statically be determined whether a given lifetime is the "owned" lifetime. Expressions transferring ownership imply that the reference being taken has ownership and that the value being assigned has ownership. The expressions that transfer ownership are:

1. Move expressions `move x`
2. For non-copy value types, all bare variable references `a`. That is, they are not taking a reference to it.
3. Functions returning ownership of a reference type or returning a non-copy value type.

Additionally, the move expression implies that the variable being moved out of must have a lifetime type of owned.  Thus the language can assign a lifetime even if it is unknown to each variable type and then infer and check these in the borrow check phase after lowering to IL.

### Ref Types and Borrow Checking

A reference type implicitly has a distinction between the reference and the object being referenced. So it makes sense to think of the type of this as `T|a` where `T` is a reference type and `a` is a lifetime. For value types, they are always owned by their variable binding and so don't have a lifetime in their type. That is the same as Rust. However, one can take a reference to a variable, and then it becomes important how long that variable lives. There is now a distinction between the reference and the value. The possible combinations are (we use `int` as a representative value type:

* `ref int|a` (should this be `ref let|a int` instead?)
* `ref var|a int`
* `ref var|a T|b` where `T` is a reference type
* `out|a int`
* `out|a T|b` where `T` is a reference type

Note that the type `ref T|a` where `T` is a reference type doesn't make any sense because an extra level of indirection has been added without benefit. Though perhaps it is possible for a type like that to be created using generics?

Here the lifetime occurs after the variable to indicate that it is the lifetime of the variable that holds that value. For example, if the value were to be moved out of the variable, that would invalidate this reference. The exception is `ref int|a` where the variable is elided. However, it might make more sense to give this the type `ref let|a int` for clarity and consistency. If that is done, the ref keyword could possibly be dropped from the types so that they become:

* `let|a int`
* `var|a int`
* `var|a T|b` where `T` is a reference type
* `out|a int`
* `out|a T|b` where `T` is a reference type

But it is a little confusing that one needs to use the ref keyword to create them and that they don't look like references. Given the expected rarity of ref types, it might make sense to always require the `ref` keyword anyway as:

* `ref let|a int`
* `ref var|a int`
* `ref var|a T|b` where `T` is a reference type
* `ref out|a int`
* `ref out|a T|b` where `T` is a reference type

Of course, more complex types like references to references can be constructed. For example, `ref var|a ref var|b int` is a double reference. But notice that in other cases the resulting type doesn't make any sense. For example, `ref let|a ref let|b int` can be collapsed to a single direct reference and so does `ref let|a ref var|b int`.

### `var` and Borrow Checking

For simplicity, variable bindings are given a single type that must hold throughout its use. So that for example, are variable with type `T|owned` must have ownership over any object assigned into it. Conceptually, one could imagine that each assignment to the variable is like a let rebinding with a possibly different lifetime. This is something like flow typing. However, that could lead to great complications when the control flow causes the same variable with different lifetimes to be unified. Thus for now, this is not done, and a variable is given a single lifetime type.

### Pseudo References

While theoretically, one could imagine taking references to pseudo references as if they were values types, they are treated as if they were actually reference types. If this were not done, and the first syntax form is adopted there would be a syntactical conflict. For example a string might have the type `string|a` but then a reference to a string the type `ref string|a` and it is now unclear whether the lifetime `a` should be for the variable being referenced, or the data the string references.

### Moving Borrowed Data

The borrowing rules enforce that only a single mutable reference can be accessible at a time. They also enforce that multiple read only references can be accessible but that no mutable reference can be when that is the case. Interestingly though, they do not prevent moving these references so long as the lifetimes work out. This is something that Rust does not support. As an example of something that would be safe:

```adamant
public test() -> void
{
    let t = mut new Test(); // t: mut Test$owned
    let x = t;
    // Now `t` is read only because of the reference x to it. However, we can still move the test object
    let t2 = mut move t; // t2: mut Test$owned
    // `t2` now has ownership of the object, but the reference `x` is still valid and the lifetimes work. Once `x` goes out of scope, `t2` will be a unique mutable reference to it.
}
```

## Lifetimes in Function Parameters

Function parameters require that the lifetime relationships be explicit. The syntax previous thought of was to reference the lifetime of another parameter. Consider a function that selects an owned string field from an object:

```adamant
public select(t: Test) -> string|t
{
    return t.field;
}
```

Here the type implies that the return value has the lifetime of the `Test` object referenced by `t`. This is in some sense true, however it could be argued it is actually false because the lifetime of the return might actually be shorter than that of the `Test` object. In fact, what is being expressed here is a bound on the lifetime of the return. So a signature of `select(t: Test) -> string <=|t` might be more appropriate. Taking a more Rust like approach it would seem you could write `select[ |a](t: Test|a) -> string|a` however this also seems incorrect. It implies that the test object and the string have the same lifetime. This is exactly what is confusing in Rust. In Rust this actually means there is a bound on lifetimes of the objects and references. Correctly rewriting this would then be `select[ |a](t: Test >= |a) -> string <=|a` but that seems quite verbose. An additional problem with this syntax is that it favors the strictly greater or lesser case which is valid but should not be the default.

Syntax idea, lifetime comparison. Here I have switched to `$` to mean lifetime to free up the vertical bar `|` for other uses. The previously used tilde `~` looks awkward in these constructs and may be difficult to type. Supposedly, many encodings have dollar sign even when they don't need to so it is an ok character to use.

```adamant
public select(t: Test) -> string$< t
{
    return t.field;
}

public select[$a](t: Test$> a) -> string$< a
{
    return t.field;
}
```

Operators are then:

| Operator | Meaning                                |
| :------: | -------------------------------------- |
| `$a`     | Names a lifetime `a`                   |
| `$<`     | Lifetime less than (or equal)          |
| `$>`     | Lifetime greater than (or equal)       |
| `$</=`   | Lifetime less than but not equal to    |
| `$>/=`   | Lifetime greater than but not equal to |

There is the even thornier issue of having multiple variables with related lifetimes. For example:

```adamant
public longer(s1: string, s2: string) -> string$< s1 $< s2
{
    return if s1.length >= s2.length
        => s1
    else
        => s2;
}
```

Here `string$< s1 $< s2` seems to imply that `s1` is less than `s2` which is not what is intended. I think we here need set operations like intersection. So `string$< s1 $& s2` or `string$< s1 && s2` or even `string$< s1 ∩ s2` would be options. Using an explicit lifetime parameter, this could be:

```adamant
public longer[$a](s1: string$> a, s2: string$> a) -> string$< a
{
    return if s1.length >= s2.length
        => s1
    else
        => s2;
}

// or even
public longer[$a](s1: string, s2: string) -> string$< a
    where a$< s1 && s2
{
    return if s1.length >= s2.length
        => s1
    else
        => s2;
}

```



---

## Old Notes

## Scopes

Scopes are the lexical regions that variables and values are available in.

### Variable Scope

The scope of the variable is the lexical region of the code it would theoretically be valid to refer to it in. Borrow checker rules can make some uses of a variable within its scope illegal. In most languages, variable scope is the only limitation placed on variable usage by the compiler.

A variable's scope starts on the statement following the variable declaration and continues to the end of the containing block or the variable is redeclared. A variable is redeclared when in the same block it was first declared in, a new `let` statement declares a variable with the same name. Note that this means a variable is not in scope in its initialization expression. However, it is in scope during the initialization expression of its redeclaration.

For `if let` expressions, the variables declared are scoped to the body of the if expression.

For `for` expressions, the variables declared are scoped to the body of the for expression. Note that each iteration of the loop is a separate instance of the variable as if it had been declared inside the loop.

Note that the rules preventing shadowing of variable names simplify the discussion of variable scope because it is not possible for a variable to be temporarily shadowed and then become available again.

It is an error to use a variable outside of its scope.

### Global Scope

Global variables are declared outside a function or class. Their scope is the entire program text. However, they may be shadowed by local variables so that a simple usage of their name does not refer to them. It is still possible to use a package or global scope qualifier to refer to them (i.e. `::`).

### Field Scope

A class or struct field can only be accessed using a member access expression. Its scope is any code which can legally access the field.

### Value Scope

A variable may have one or multiple values during a single execution of its containing block. Immutably bound variables will only have a single value. Mutably bound variables can be assigned a new value.

It is important to understand that the value be discussed here is the value assigned into the variable. For value types, this is the value of the object. However, for reference types this is the *reference* not the object being referenced. Likewise for pointer types, it is the pointer, not the object being pointed at.

A value's scope begin's at the initializer or the assignment expression that assigns the value to the variable and continues until the variable is assigned again (even if to the same value i.e. `x = x`), the value is moved out of the variable or the variable's scope ends. Because variables can be assigned down different code paths, different value scopes of a variable can overlap.

It is an error to use a variable outside of one of its value scopes. That is before it has ever been definitely assigned or after the value has been moved out without a new value being assigned,

## Liveness

A value is live from the first expression that uses that value of the variable to the last expression that uses it. Note that values that are assigned but never used are never live.

## Lifetimes

The lifetime of a value is the span of time it exists/is valid during an execution. Here we are thinking of both the *reference* and the *referent* as two separate values with different lifetimes. Obviously, each execution will have different concrete lifetimes for each value. However, there are always going to be certian relationships between the lifetimes of those values. For example, a reference should never outlive its referent. Thus when we discuss lifetimes, we are discussing *abstract lifetimes*. Those are an abstracted notion of the potential lifetime of a value a variable might have or reference.

The borrow checker enforces a set of constraints on the relationships between values and uses these to determine at what points in the code objects should be deleted.

---

Destructors don't have access to borrowed references, only to owned references and value type fields. But what do you do about objects that have borrows inside of them?

```adamant
{
    let x = mut new Borrowed();
    let y = new Outer(mut x);
    // It is supposed to be valid for the compiler to delete `y` before `x`
    // implicit: delete y;
    // implicit: delete x;
}

// Given
public class Borrowed()
{
    private let calc_value: int = 6;

    public calc(self) -> int
    {
        // Accesses fields of borrowed, not safe to call on a deleted object
        return calc_value;
    }
}
public class Outer()
{
    private let value: mut Inner; // implicit: ~own

    public new(b: mut Borrowed~self)
    {
        // It is allowed for us to pass self, because even though our destructor
        // is called before Inner's we live at least as long as it
        self.value = new Inner(self, mut b);
    }

    public delete(self)
    {
        let x = value.calc(); // supposed to be allowed because we own value
        // implicit: delete value;
    }
}
public class Inner()
{
    private let outer: Outer~self;
    private let value: mut Borrowed~self;

    public new(self.outer, self.value)
    {
    }

    public calc(self) -> int
    {
        return value.calc();
    }

    // Not needed, not having it here wouldn't change the issue
    public delete(self)
    {
        // Can't access `value` or `outer` because they are borrowed and may
        // live only just as long as we do, not strictly longer
    }
}
```

Note that a very similar issue could be created by passing a parent node several
levels down a tree.

Could I further restrict the rules. You can only access:

* ~own or ~static structs // If an ~own struct could hide a ~self borrow, still a problem
* ~static references

That breaks the case of a buffered writer that needs to clear its buffer. It very well may own the writer it is writing to, but if its destructor can't write to it that prevents it from flushing the buffer. But there, the writer has its own destructor. Would it be ok to make it so only types that marked themselves as explicitly ok for use during destruction were accessible? How would they mark themselves that way? They would need to promise not to hold `~self` references or objects that weren't safe for destruction. It would be a real pain if you found yourself needing a type in a lib that hadn't been correctly marked as such. The opposite might be a better default.

Maybe the self lifetime is somehow dealt with specially kind of like the `Self` type would be? Like you must explicitly say you are using it, but then don't have to specify what it is.

```adamant
public class Borrowed()
{
    private let calc_value: int = 6;
    private var outer: Outer?~self = none;

    public new(self.outer)
    {
    }

    public calc(self) -> int
    {
        // Accesses fields of borrowed, not safe to call on a deleted object
        return calc_value;
    }
}
public class Outer()
{
    private let value: mut Inner; // implicit: ~own

    public new(b: mut Borrowed~self)
    {
        // It is allowed for use to pass self, because even though our destructor
        // is called before Inner's we live at least as long as it
        self.value = new Inner(self, mut b);
    }

    public delete(self)
    {
        // Now, even though we own value, because it uses ~self it isn't safe
        let x = value.calc(); // supposed to be allowed because we own value
        // implicit: delete value;
    }
}
public class Inner()
{
    private let outer: Outer~self;
    private let value: mut Borrowed~self;

    public new(self.outer, self.value)
    {
    }

    public calc(self) -> int
    {
        return value.calc();
    }

    // Not needed, not having it here wouldn't change the issue
    public delete(self)
    {
        // Can't access `value` or `outer` because they are borrowed and may
        // live only just as long as we do, not strictly longer
    }
}
```

But can you then hide the `~self` behind another type

```adamant
public class A
{
    public let b: B;

    public new(self.b) { }
}
public class B
{
    public let c: C; // B must either specify the self lifetime, or say it uses the self lifetime
    public let c: C~static; // B does not need to worry about the ~self because it is borrowing

    public new(self.c) { }
}
public class C[~self]
{
    public let x: X~self;
}

// That then means there are cases where the lifetimes force an order of deletes right?
```

Can we do a parent ref without the ~self lifetime in the child?

```adamant
public class Tree_Node[~parent, T]
    // implicit: where ~parent >= ~self
    // Should that NOT be >= or else every type with a lifetime parameter could be an issue?
    // `where ~parent > ~self` would make this code valid?
{
    public let parent: Tree_Node[~parent]?~parent;

    public let value: T;

    // Note that ~own < ~self <= ~parent
    public let children: mut List[Tree_Node[~own, T]] = new List();

    public new(value: T)
    {
        self.parent = none;
        self.value = value;
    }

    public new(parent: Tree_Node~parent, value: T)
    {
        self.parent = parent;
        self.value = value;
    }

    public add_child(mut self, value: T)
    {
        // valid because self : ~self > ~own
        // invalid because it would last longer than the borrow of self we have
        self.children.add(new Tree_Node(self, value));
    }

    public delete(mut self)
    {
        // using `parent` prevented because if could be that ~self == ~parent
        // using `children` is allowed but shouldn't be. They could walk up the
        //  parent chain in a method call.
        // The self type failed to be adequately infectious here
    }
}
```

***** This next one seems to work: (Or does it?)

```adamant
public class Tree_Node[~self, T]
{
    // Does the type need to be `Tree_Node~self?`
    //public let parent: Tree_Node?~self;

    // The parent needs to remain mutable even after we have a reference.
    // The Read_Only type indicates that and tells the system that we are not
    // thread safe?
    public let parent: Read_Only[Tree_Node]?~self;

    public let value: T;

    // Can `[~self]` be inferred or implied because it is self?
    // Is ~self even correct there? Since this is publicly mutable, what if someone
    // else were to add an object to it? Could they even do it given the current type?
    public let children: mut List[Tree_Node[~self]] = new List();

    public new(value: T)
    {
        self.parent = none;
        self.value = value;
    }

    public new(parent: Tree_Node~self, value: T)
    {
        self.parent = parent;
        self.value = value;
    }

    public add_child(mut self, value: T)
    {
        // `self` has lifetime `~self`
        self.children.add(new Tree_Node(self, value)); // inferred Tree_Node[~self]
    }

    public delete(mut self)
    {
        // using `parent` prevented because it has lifetime ~self
        // using `children` must be prevented because it is a type taking ~self
    }
}

public class Tree[T]
    where T: Ordered
{
    // Using `~self` here would mean we need to declare the use of the ~self lifetime
    // It would also mean we couldn't use the tree in our destructor.
    // It must be that ~own < ~self so that we can be said to own this and use it
    // TODO should ~own be inferred to ~self because I own the root reference?
    private let root: Tree_Node[~own]? = none;

    public add(value: T)
    {
        // Add the tree node in the correct place in the tree and balance
    }

    public delete()
    {
        // Tree could now be used, because the self lifetime is bound to ~own which is valid
    }
}
```

Maybe I need a special lifetime that indicates a value only lives after the constructor and before the destructor. i.e. some kind of ~init marker. It would then ....

---

Rust non-lexical lifetimes treat things that implement drop differently, forcing them to be in order unless they have the may dangle attribute. I could do something similar. Except I would use the equal lifetimes ability. This may be important for things like using destructors for transaction scope. Consider:

```adamant
{
    let transaction = connection.begin_transaction(); // destructor will rollback
    let nestedTransaction = connection.begin_transaction(); //?
    transaction.commit(); // consumes the transaction, there by destructing it and preventing rollback from an exception after
}
```

Actually, it seems like there are some easy patterns. make the nested transaction be generated off the outer one.

Consider this set of rules:

* To use a field in a destructor, it must not be borrowed with a lifetime that could be equal to self or contain borrows with a lifetime equal to self.
* By default a lifetime parameter is implicitly >= ~self. That is it could be equal to self and can't be used in a destructor.
* Declaring a parameter of type ~self is strange, because passing a lifetime parameter value for it doesn't seem to make sense. What would it mean to pass a larger lifetime? If passing a value for it is forbidden, then it doesn't allow you to override the lifetime and make it safe to use in your destructor.
* WRONG: "A lifetime parameter is required for references, but when using a type, the lifetime is inferred to be a new lifetime ~x which is equivalent to the containing variable's lifetime?" This fails to work because it hides borrows that may live only as long as self.
    ```adamant
    public class Wrapper
    {
        private let foo: Something~own; // implicitly Something[~foo]
    }
    ```
* The lifetime parameter ~default (~borrow?) is special, it will be used as the lifetime parameter of all fields (just the ones that are also ~default?). This makes it easier to declare that a class holds borrows without using the lifetime all over the place.
* To be allowed to use a borrowed value or value with a borrow in it during destruction, specify its lifetime to be something that strictly outlives self. This can be down with `where ~a > ~self`. That is even allowed on ~default (~borrow) as `where ~default > ~self`.

---

Idea: solve both construction and destruction issues with lifetimes. By default a lifetime parameter ~a has `where ~a >= self` meaning that it could be constructed and destructed at the same time. So it can't be used in the constructor or destructor. One can further constrain this by adding one of:

* `where ~a > ~self` ~a can be used in the constructor and destructor
* `where start(~a) > start(~self)` ~a can be used in the constructor, but not the destructor
* `where end(~a) > end(~self)` ~a can be used in the destructor, but not in the constructor

SEE "Make dropck "see again" via (focused) where-clauses" in https://github.com/rust-lang/rfcs/blob/master/text/1327-dropck-param-eyepatch.md

The Rust guys already thought of something like this for destructors

Is there then a way to use this notation to call other functions from inside a constructor or destructor?

---

Can you allow parent references using promises?

```adamant
public class Tree_Node[T]
{
    private parent: Promise[Tree_Node[T]]?;

    public new(parent: Promise[Tree_Node[T]])
    {
        // You could still cause a deadlock by awaiting parent. Unless new can return a promise?
    }
}
```

Maybe the rule is that a lifetime == to self can't be used in either the destructor or the constructor? But it seems like you might want to be able to say something can be used in the constructor but not the destructor or vice versa.

---

Rule: the moment the destructor is entered, an object's lifetime needs to be over. Note that to call a destructor, you basically need to have ownership over the object.

---

Does Rust allow borrowing a redeclared variable beyond its redeclaration? Yes it does!

```rust
{
    let x = 5;
    let y = &x;
    let x = 2;
    println!("{} {}", x, y); // is this allowed?
}
```

---

I need a good set of lifetime test cases. Possible test cases include:

* Database Transactions
    ```adamant
    {
        let t1 = connection.begin_transaction();

        t1.commit(); // t1 can't be destructed until after this line except during an exception

        // This requires that either the compiler destruct t1 before this line
        // or that t1.commit() consumes the value of t1 and deletes it.
        let t2 = connection.begin_transaction();

        t2.commit();
    }
    ```
* C# Style "Linked List" Collection (i.e. an encapsulated linked list)
* Tree with parent links
* Sorted Set used implemented using a red-black tree

---

Consider using <- for assignment. This is clearer and +<- and /<- becomes clear with /= meaning not equal.

exit instead of break?

---

The ownership tree forms a safe tree of contexts. If you have ownership or mutability then everything indirectly owned by it is an isolated context

---

For constructors, the reason for the init then call base is after the base call the object should be valid and references can be given out and methods called. Invariants should be checked for all subclasses at the top of the constructor call chain to ensure it is a valid object at that moment.

---

Objects that aren't Sync in the Rust sense should only be accessible by a single thread at a time. Thus, it should be safe to relax their rules about having multiple multiple references (except for the enum problem).

---

Since a context, owner or guardian object could be established, it seems like the shared mutability of the objects can be established. For example the nodes of a doubly linked list. But how does one ensure the lifetime constraints to avoid dangling references? For example, to prevent an error during node removal from the doubly linked list where one of the other nodes still has a reference? It seems like that needs a more flexible idea of lifetime, or reference counting.

---

If there were a concept of bi-directional link between objects, then it would be possible to know when they are all severed and it is ok to delete one. Those could be enabled when they share an owner, but note, this amounts to reference counting, we are just hiding the count in the link count.

---

Having auto immediate owner references would give me parent references in doubly linked lists, trees, and DAGs.

---

If object A has a `let b: B ~own` reference to object b. Then there is a permanent object lifetime relationship that `~a >= ~b` regardless of scopes etc.

---

A reference to your owner/guard would be valid except it would prevent combining owners.

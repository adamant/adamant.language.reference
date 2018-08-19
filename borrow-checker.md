# Formal Description of the Borrow Checker

This section attempts to give a more formal and precise description of how the borrow checker works. This is both for programmer's understanding and to aid in implementation. Please note that this section makes careful distinctions between similar sounding terminology.

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

For constructors, the reason for the init then cal base is after the base call the object should be valid and references can be given out and methods called. Invariants should be checked for all subclasses at the top of the constructor call chain to ensure it is a valid object at that moment.

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

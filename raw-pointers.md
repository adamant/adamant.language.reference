# Raw Pointers

`* T` and `* mut T`. Note that Rust uses `*const T` for the first. Why is that when, they don't use `const` with other types. Also allow `*void` and `* mut void`.

## New and Delete for Pointers

Instances can be explicitly allocated on the heap and deleted from the heap using `new*` and `delete`.

    let x = new* Point(1, 2); // x: *~own Point // TODO order of * vs lifetime?
    unsafe
    {
        delete x; // since x is owned, this takes ownership and makes it mutable
    }

## Allocating Raw Memory

    let x = new(45); // allocates 45 bytes, x: *void
    unsafe
    {
        delete(45, x); // must pass exact same arguments? free doesn't need memory size
    }

## Allocating Memory For Arrays

    let x = new<Point>(10); // allocates memory for 10 Point objects, x: *Point
    unsafe
    {
        delete(10, x); // since x: *Point it knows to calculate 10 points
    }

## Placement New and Manual Destruction

    // Allocate memory
    let x: mut = new<Point>(2);
    unsafe
    {
        // Call Constructors with Placement New
        new(x) Point(1, 3);
        new(x+1) Point(9, 4);
        // Call Destructors
        (x+1).delete(); // only valid on pointers
        x.delete();
        // Deallocate memory
        delete(2, x);
    }

## Allocating Out of Pools

This seems less useful if it is limited to pointers and can't be used with references and lifetimes.

    let pool = MakeMemoryPool(); // defined somewhere
    let x = new(pool) Point(6, 5); // allocate the point out of the pool
    delete(pool) x; // undefined if you don't pass the pool here

Perhaps the way to do this is to wrap the types.

    let x = new Pooled<Point>(/* how to call constructor? */);

## Resizing Memory

**How to resize memory?**

    var x = new<int>(45);
    x = new<int>(x, 45); // resize x? do you need to pass the old size? 
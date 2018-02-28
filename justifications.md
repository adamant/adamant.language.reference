# Justifications

This document gives justifications for many of the design decisions of Adamant. The reason this is useful is because it is so easy to forget why a certain design is necessary and begin to think of changing the design in a way that actually won't work.

## Principles

There are a number of design principles that are followed throughout Adamant and used as justifications for many design decisions. This section defines those, rather than repeating their definitions below.

### Allow for Future Expansions

Design things in restrictive ways that allow for those restrictions to be lifted in the future if it makes sense. Also, ensure syntax and semantics would be orthogonal to likely future enhancements to the language.

### Be Explicit

Require explicit declaration of things to provide clarity to the reader and a safety check. For example, a class must be declared `abstract` if it has any abstract methods even though the compiler could just implicitly assume the class to be abstract in that case.

### Best Practice Defaults

If there is a clear best practice then that should be the "default" i.e. require no extra keyword or code. For example, the default is that methods are overridable (unlike C++ or Java that require the `virtual` keyword). Another example is the default being immutable and making things mutable requiring the `mut` keyword.

### Do Not Lock in an Implementation

Make design choices that enable as many implementations as possible. We don't want to lock Adamant into a particular approach to implementing the language that may have performance drawbacks.

### Only One Way

Do not provide multiple equivalent ways to do something or express something.

### Support Program Evolution

Code changes over time, the language should make that process as smooth as possible. For example, requiring explicit override or hide keywords allows for a clear handling of the situation where a method is added to a base class that matches a method that was previously present only on a subclass.

## Design Decisions

### All Methods Must Have a Unique Implementation

Given a method `M` in interface `A` and interfaces `B` and `C` that implement `A` and override `M`, a class `D` that implements `B` and `C` must override `M` to provide a unique implementation of `M` to be called. This applies even if `B` and `C` rename `M`. At first glance this seems a strange restriction to place. However, since Adamant is trying to not lock in an implementation, this restriction ensures certain implementations are possible, and can be safely removed in the future if desired. Specifically, it enables a vtable implementation where every method across all classes is assigned a unique slot and there is only one vtable pointer in an object. If this restriction were not in place, it would then be ambiguous which method pointer to put in the `M` vtable slot for class `D`.

### `internal` Access Modifier

Several other keywords were considered instead of `internal` for that access modifier. The internal keyword doesn't clearly indicate internal to what. For a while `package` was used instead. However, package reads as if one is actually declaring a package. The synonyms of internal `limited` and `restricted` were also considered. They have the same problem of not clearly indicating what unit they are relative to. Given that, it seemed best to stay with `internal` for familiarity to C#.

### New Operator

Any value with mutable state should be created with the `new` operator. Rust doesn't have a new operator. However, it was decided that for Adamant the explicitness of having a new operator for allocating memory was good. It then might seem to make sense to not use new for structs. Using new for structs in C# has always felt a little weird. However, the optimizer of Adamant is expected to frequently convert reference types into stack allocations so they might end up equivalent. Also, it make structs consistent. And it would seem weird to declare struct constructors using new but then call them without new. One can't just have static functions build structs because of safety around definite assignment.

### No Python Style Loop Else

Python's loop else condition runs after the loop as long as break was not used. A feature like this was considered, though it would have needed a better keyword. However, that construct is more useful when selecting an item out of a collection, and in Adamant this will be done with the `for` loop that. Since `for` loops operate on iterators, using a filter on the iterator is a better and easier way to do this. Also, it wasn't clear how to combine such a feature with the existing loop else feature. If it were added, a keyword sequence like `if not break` might be good.

### Numeric Types

Both Rust and Go use integer values to indicate the bit length of types. However, C# did not and it was already planning for 64 bit architectures.
The numeric sizes were selected based on the following criteria:

1. Anything larger than 64 bit pointers is unlikely any time soon
2. Old 32 bit machines could have 64 bit pointers
3. We want to be friendly to embedded processors that might be special
4. We want standard sizes to be nice
5. Platform dependent sizes cause issues

The default sizes (i.e. those that leave off the bit length) were chosen as a compromise between small enough memory use and enough digits of precision to be useful. Hence 32 bit integers with their more than 9 digits were chosen as the default integer size while 64 bits were chosen for floats and decimals because their 32 bit versions had only 7 digits of precision.

### Tuples

Generally, I'm not a fan of tuple types. I feel like their unnamed fields are an anti-pattern leading to confusing code. However, they are frequently used in languages that have them. Probably too frequently used. It wouldn't be hard to create tuples in Adamant by declaring a `Tuple` type overloaded on the number of type parameters or with a `Tuple<Values...>` type. That would certainly be done if they weren't included in the language. Additionally, lists of types are essentially tuple types so it will make working with generics easier. Given that it makes sense to include them in Adamant.

In many other languages tuples have a syntax like `(x,y)` but that seems easily confused with grouping. Also, there is no good syntax for a tuple of one value. Some languages use `(x,)` which just seems ridiculous. Given that the new operator is being used for all values including structs, tuples should be declared as `new (x,y);` and would not be ambiguous (that syntax could be an issue for placement new). However, this would still not be a very distinct syntax for tuples. For example, a new single tuple would be `new (x)` and new empty tuple would be `new ()`. Since Adamant does not have primitive arrays, the square bracket symbols are available for use with tuples. This provides a much more distinct syntax and has precedent in other languages. For example the language [E](https://en.wikipedia.org/wiki/E_(programming_language)) uses them for tuples.

Tuple element access using square brackets could be confusing. For example, `new [x, y][0]`. Also it makes it seem tuples could be accessed with non-constant indexes and would encourage people to declare named constants for tuple indexes. It makes more sense to go with the admittedly ugly Rust style of using integer field names like `t.0` and `t.1`.

### Reference Recovered Mutability

An interesting situation can occur.

```adamant
    public Swap(a: ref var Point, b: ref var Point) -> void
    {
        let t = move a; // TODO does this need to be move *a or something?
        a = move b;
        b = move t;
    }

    var a = mut new Point(1, 1) // a: ~own mut Point
    var b = new Point(2, 2); // b: ~own Point
    Swap(ref var a, ref var b);
```

No one can mutate the object originally referenced by `b`. Yet, the swap function which can't mutate the values of either of its arguments is able to move object referenced by `b` into `a` thereby allowing the calling code to mutate it. This seems counter intuitive at first. However, since both references own their objects, the swap is simply recovering the mutability through changing ownership.

### Value Types

The `struct` keyword is used to define value types. Originally, Adamant was not going to have value types, but only reference types declared with `class`. This ran into several issues.

First, it was unclear how to interop types with C without complicated marshalling since all types could have a vtable. There were also issues about subtyping since any type could be subclassed. How would one handle a subclass of `int` being passed to an external function? Still it would have been possible to apply the `external` keyword to class declarations and restrict external classes to have no base class and no subclasses.

Second, there were problems around combining move and copy semantics into reference types. For example, consider a reference to a `var` containing a class with move semantics. What if one were to assign a different instance into it that was actually a subclass. Since they would be different sizes, that could never be implemented unless types with move semantics were still stored as references to the heap. Of course, the compiler could probably optimize that away in many cases, but it seemed foolish to rely on that so much.

Third, in implementing low level functionality like reference counting it seemed really useful to have a type that could be certain to exist on the stack as a value type.

### Control Flow Requires Blocks not Parenthesis

Control flow like `if`, `for` etc. require block statements and do not have parenthesis around the expression just like in Rust. Originally, control flow was C style where the expression portion must be surrounded with parens, but the statement can be any statement. The current approach solves the dangling else issue and also makes loop else unambiguous (i.e. people won't think the loop else is for an if). More importantly, I think this makes scopes clearer. Whatever effect scopes have on value lifetimes, they will be visually apparent in the code. With C style control flow, there is a scope introduced, but it may not be visually apparent when it is not surrounded by curly braces.

### `loop` Keyword

As in Rust the justification is that the control flow analyzer treats it differently. Also, just makes the thing clearer.

### Iterators instead of Enumerators

Iterator seems to be the more standard term. It isn't clear that "Enumerable" and "Enumerator" are really clearer.

### Optional Type

Different languages use different names for option types (see [Wikipedia Option type: Names and definitions](https://en.wikipedia.org/wiki/Option_type#Names_and_definitions)). At first, the C# convention of referring to them as nullable was followed because it was familiar. It also seemed to make clear that developers should expect that for reference types it would be represented as efficiently as a null pointer. However, the name "nullable" seemed to carry with it too much history and implied that they were bad or dangerous. A more neutral name seemed appropriate. Looking at the options, it seemed that "optional" was the clearest. Both Java and Swift name option types that and they are two of the languages most concerned with clear names. Like Swift the `T?` syntax means it isn't necessary to write out the name optional normally.

When considering what to name the two values of an option type, `Some<T>` and `none`. Seemed the clearly better choice.

### Naming Conventions

Originally, the naming conventions were:

* Mutable Class Names, Type Parameters: Mixed_Case_With_Underscores
* Immutable Class Names (including attributes): snake_case
* Namespaces, Functions, Public Fields, Static Variables: PascalCase case
* Parameters, Private & Protected Fields: camelCase

There were a number of issues with this:

* Single word immutable types looked like local variables.
* Associated functions on immutable classes could be hidden by local variables.
  ```adamant
  let lexer = new lexer();
  lexer.StaticFunction(); // error: no method StaticFunction on type `lexer`
  ```
* Fields can act as properties, so it doesn't make sense to give them different casing.
* Properties won't need to be distinguished from their backing fields as often. Use a field instead.
* Changing a type from purely immutable to mutable may not be a breaking change, but would change the naming convention.
* The distinction between mutable and immutable types may not be that important given that immutable is the default and immutable variables of mutable classes are common.

Many possible naming conventions were considered. In deciding between them, there were a number of considerations.

* Snake case has faster reading sped (i.e. easier).
* Snake case is awkward to type.
* All caps means shouting in common usage. It should be reserved for dangerous or rare things rather than used for common constants.
* It still makes sense to use a wider variety of casing conventions, but single word names mean that initial case must distinguish things that are truly important to distinguish.
* Acronyms have always been an issue in C# style naming conventions
* Namespaces only rarely occur inside code so it is less of a concern if they conflict with variables. Having them be distinct from types and functions in using statements is more valuable.

### `=>` Operator

The expression evaluation operator was the result of trying to come up with a syntax for if expressions and after considering many different syntaxes, realizing that this was essentially the same problem as match, and Rust used `=>` for match expressions which I was likely to match just for familiarity.

Before this syntax, the use of a keyword `is` was considered. It was thought of as mirroring the exiting of functions and loops.

| Structure                 | Exit     |
| ------------------------- | -------- |
| Function                  | `return` |
| Loop                      | `break`  |
| Choice (`if` and `match`) | `is`     |

This might have looked like:

``adamant
let x = if cond
        {
            DoSomething();
            is 5;
        }
        else
        {
            SomethingElse();
            is 6;
        };

let y = if cond is "Hello" else is "World";
let z = match v
        {
            [0, y]
            {
                Action();
                is y;
            },
            [x, 0] is x,
            [x, y] is x + y,
        };
```

But using `is` for this was also inconsistent with with C#'s use of `is` for typechecking. It seemed prudent to leave the `is` keyword avalible for that.

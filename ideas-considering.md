# Feature Ideas being Considered

## Loop Else

Sometimes it is useful to execute some code if a loop is never run. This can be done with the else clause of the while and for loop.

    while condition
    {
        // do work
    }
    else
    {
        // condition was false to start with
    }

    for let x in expression
    {
        // do work
    }
    else
    {
        // no items in collection
    }

This can be useful for definite assignment. If the loop assigns a variable, it may be the case that the loop never runs and the variable may be unassigned. However, you can assign the variable in the else clause to a reasonable default so that the variable will definitely be assigned after the loop.

Note: this is different from the python style loop else construct which runs as long as the loop completed successfully.

Note: Alternatively, a different keyword or group of keywords could be used for loop else. Options include `otherwise`, `loop else`, `while else`, `for else`, or `if none`.

## Allow `!` at the End of Function Names

Scheme uses `!` at the end of functions to indicate they are mutating. That might not make sense for Adamant where mutation is probably more common and less frowned on. Rust uses `!` to indicate macros. It is nice to have a clear distinction for macros, but the syntax doesn't really strike one as clearly indicating a macro. Since is not used to mean "not" what if we allowed `!` at the end of function names and used it to indicate divergent functions?  Otherwise it might not be obvious that execution will terminate at one.

## Date and Time Literals

Add support for date and time literals. Could be done using either the backslash or single quotes.

    // With backslash
    let x = \2016-06-06;

    // With quotes
    let y = '2016-06-06';

## Tuple Base Class

It probably makes sense to have all tuple types inherit from and abstract base class `Adamant.Predefined::Adamant.Predefined.Tuple`. C# has them implement several interfaces about structural equality.

## `out`

I have `ref`, does it make sense to add an `out` keyword like C#?  Or does the ability to return tuples make `out` not useful enough?  They could be really useful with external functions (though, not sure how the memory would get cleaned up).

## `var` is `mut` for Value Types

Currently, `var` and `mut` have distinct meaning for value types. It is assumed this will be needed to implement certain pseudo references. Also, on struct constructors it makes sense to have a `ref mut self` parameter. However, perhaps using `var` should always imply `mut` for mutable structs. Logically it seems if you can assign a new value you should be able to mutate the value.

## Owned borrows

There may be situations where a class wants to borrow something, but we want to actually give it ownership. One way to implement string would be for `string` to borrow the `Unsafe_Array` it contains. That allows substring to work correctly. However, there will need to be strings that own their `Unsafe_Array`. If we could pass an owned reference where are borrow was expected, then that would be handled. Of course, this may require pervasive drop flags adding complexity and hurting performance.

My experience writing the sequence predict code in Rust indicates that this might be very necessary. There were many places around iterators where a value wouldn't live long enough for some borrow. If ownership could be handed into a borrow, that would not be an issue.

The "High Line" pseudo Adamant code seemed to indicate the need for owned references that were bounded by some lifetime. This is similar I think to Rust `Box<'a T>`. However, owned borrows might eliminate this because they could just be borrows that happen to own the value.

## Returns That Can't be Ignored

Part of the Midori project's safety came from not being able to ignore the return value of a function except by explicit statement. This had the effect of ensuring that return codes and error results couldn't be ignored. If my error model includes `Result<void>` then it is possible people could ignore that. Perhaps there should be some special handling to say that certain types shouldn't be ignored when returned.

For example, there was a bug in the compiler where `ExpectToken(PublicKeyword);` should have been `children.Add(ExpectToken(PublicKeyword));`. If return values couldn't be ignored, this would have been a compiler error.

Any function that is a pure function should not have its return value ignored because you can't be calling it for side effects.

The syntax of `ignore foo()` is confusing because it seems you are ignoring the function call rather than the return value.  Another possible syntax would be `_ = foo()`. That is consistent with the use of underscore as a placeholder that isn't used.

## Units of Measure

It would be really good to be able to have good units of measure either directly in the language or as a really clean library. This might be a useful place for an effect that says all code uses units of measure.

## `else match`

Allow a match to occur after an else.

## Alternative Pointer Syntax

While C/C++ have well established pointer syntax and Adamant is in that linage, it will use pointers much more rarely. So it may be acceptable to change the pointer syntax for purposes of clarity or freeing up symbols. In terms of clarity, a postfix dereference operator may be clearer than a prefix one and would then combine well with the member access operator. In terms of freeing up symbols, the unary star operator and unary ampersand operators aren't actually that useful for anything else.

### `@` Means Address Of, `^` Dereferences

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `@T`
Pointer to mutable T   | `@mut T`
Address of a struct    | `@x`
Address of an object   | `@x`
Deference Pointer      | `x^`
Access Member          | `x^.y`

Note that Pascal like languages seem to allow `^` to be used as either a prefix or postfix operator. That might make certain things clearer.

### `@` Means "at", `&` Means Address Of

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `&T`
Pointer to mutable T   | `&mut T`
Address of a struct    | `&x`
Address of an object   | `&x`
Deference Pointer      | `@x`
Access Member          | `(@x).y` ?

This is confusing with C++ reference syntax. Also, it doesn't provide a clean member access unless the dot operator auto-dereferences which I've never liked.

### `ptr<T>`

Given that pointers are meant to be rare, why not say they are a special predefined type like unsafe array?

Use                    | Syntax
---------------------- | --------
Pointer to immutable T | `ptr<T>`
Pointer to mutable T   | `ptr<mut T>`
Address of a struct    | `ptr.to(ref x)`
Address of an object   | `ptr.to(x)`
Deference Points       | ??
Access Member          | `x.y`

Here the type overloads the dot operator. This is consistent with the behavior of the dot with variable references. There doesn't seem to be a good way to handle dereference here though. Perhaps there is some operator that could be allowed to be overloaded. That operator maybe should be allowed on variable reference types to get the underlying reference.

## `repeat {} while <exp>;` or `do {} while <exp>;` Loops

Rust doesn't have `do {} while <exp>;` loops. While they are rare, they do come up. In fact when writing a recursive decent parser there are quite a few. Using `loop {} while <exp>;` to avoid introducing a new keyword was considered. However, someone reading the code wouldn't know to look for the while at the end or would have to check all loops to see if they ended with a while.

## Make good use of other symbols

I should seriously consider what other symbols are unused and how they could be used to good effect. Of course, it might be good to leave some symbols for future use, but it is so hard to know if those uses will make sense for the available symbols. Possible symbols are:

* `^`
* `&`
* `|`
* `$`

And of course combinations of symbols and unary prefix and suffix versions of existing operators.

## Don't Use `new` for Structs

Despite the justification given for using new with structs and tuples, there is still enough reason to consider not doing that. Instead, struct construction could look like a static call to the constructor or to the type name. So `My_Struct` would be constructed by calling `My_Struct(value)`. However, unlike C# all structs would be required to be initialized. There would be no default struct construction. This function all like syntax reflects that fact that no heap memory is being allocating, but some construction work may be being done. It also removes confusions around trying to use the other new construction syntaxes on structs. For example, `new?` wouldn't make any sense for structs.

## Rename `for` Loop to `foreach`

The `foreach` keyword seems to read better. Compare "for child in children" to "for each child in children". The keyword `for` reads better for counted loops. Compare "for i equals 1 to 10" to "for each i equal 1 to 10". Of course, there is the mixed case "for each i in 1 to 10" which is unclear.

## Change Naming Conventions

Since fields can act as properties, there won't be the C# issue of having private fields named the same as public properties. That means we don't need separate naming conventions for them. I find I like the readability of methods and fields being lowercased in rust. Though the underscore is annoying to type. Also, I like the underscore in type names, but single word type names show no indication that they are types. It may not be a breaking change to take a type that was immutable and make it mutable. Perhaps immutable types shouldn't be distinguished from mutable ones. Maybe the naming conventions should be:

* Mutable Class Names, Type Parameters: Mixed_Case_With_Underscores
* Immutable Class Names (including attributes): snake_case
* Type Parameters: PascalCase
* Namespaces: PascalCase case?
* Functions, Public Fields, Static Variables, Parameters, Private & Protected Fields: camelCase
* Constants, Immutable Globals: ??

## Implicit Field Initialization

What if any constructor argument would automatically initialize a field named the same, if that field wasn't otherwise assigned into? Or maybe there would be some other shortcut to eliminate the constructor boilerplate.  `self.fieldName`

## Omit `-> void` for Procedures

Instead of requiring that procedures have `-> void` in their declaration, just leave it off. This is what Rust does. It also reduces noise and boilerplate. The one disadvantage is that `void` is still a valid type and it kind of obscures the role of the `void` type.

One way to do this would be to add a `function` or `func` keyword in front of function declarations similar to Rust's `fn` keyword.  This would then make function declarations unambiguous even without the `-> void`. Note that operators and properties would not be preceded by `function` as they already have `operator`, `get`, or `set` in front of them. This would make all declarations consistent in that they would be a keyword followed by a name. Alternatively, the keyword could be `method`? But then you would still need `function` for non-methods.

## Generic Syntax Clarity

Apparently, Java allows generic methods to be called like `instance.<String>foo()`. That removes some ambiguity. If the declaration syntax matched. For example, the declaration could be `function<T> foo() -> void`. There may however be a problem with classes where the generics need to come after the type name. Also, it just reads strangely to have the generics before the method name.

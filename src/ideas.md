# Appendix: Ideas

This appendix describes various ideas for features that could be added to the Adamant language in the future. Sometimes, it clarifies why a particular feature is currently not in the language.

Sections:

* [Operators](#operators)
* [Preprocessor](#preprocessor)
* [Documentation Comments](#documentation-comments)
* [Expressions](#expressions)
* [Types](#types)
* [Named Parameters](#named-parameters)
* [Development Aids](#development-aids)
* [Generics](#generics)

## Operators

### Symmetric Operators

Allow binary operator overloads to be declared as "symmetric". This allows them to be called with their arguments in either order. Thus when overloading an operator for two different types, one doesn't need to overload it twice, once for each order of types, but can simply write one function which will be used for both orders. This idea comes from JAI where not only operators, but any function of two arguments can be declared symmetric. If the function is meant to represent a mathematical operation, then that makes sense, but seems odd otherwise.

### Comparison Chaining

Make `a < b < c` legal. This would also allow `a < b > c` which seems confusing. Perhaps there should be rules that the direction of comparisons can't change in a chain. So `a < b <= c` and `a > b >= c` are legal but `a < b >= c` and `a > b <= c` aren't. An equal would be allowed in the middle: `a < b == c < d`. What about not equal? That seems confusing. What does `a < b =/= c < d` mean?

### Advanced Operator Overloading

A number of additional options for operator overloading are possible. One could allow overloading of additional symbols and sequences of symbols. Such operators could have no precedence, or the programmer could be allowed to specify their precedence. Additionally, one could declare an operator to be commutative. This would be similar to the idea of allowing symmetric operators. It would mean that overloads of the operator could have there arguments passed in either order. Of course, with imprecision of number types, that could change behavior. Perhaps it would also make sense to allow the programmer to specify the associativity of new operators.

### Operator Partial Order

Instead of having a total order of precedence on the operators, have only a partial order. So if two operators had no relative precedence, it would be an error to use them without disambiguating parentheses. An example of where this could be helpful is the "`xor`" operator (see "`xor`" Operator idea). Note that this may not be a true partial order. Rather precedence may be a DAG. For example, if library "A" declares an operator to be higher precedence than equality and library "B" declares one to be lower precedence than equality, that doesn't mean the two operators should have a relative precedence.

### "`xor`" Operator

Use "`xor`" as the logical exclusive or operator. It could have no precedence relative to the "`and`" and "`or`" operators. It is unlikely anyone would know the precedence of it. In fact, there may be disagreement about the correct precedence. This has been omitted from the language for now to avoid imposing a precedence relative to the "`and`" and "`or`" operators before operator partial ordering is supported.

### Dot Product and Cross Product Operators

These seem like fairly standard useful operators. They may not be defined on the primitive types, but they could probably exist and be given precedence that mathematicians would expect.

### Use `<<` and `>>` as Operators

These character sequences are now available in the language and seem like they are evocative of operations like directing data etc. They could be useful.

### Checked Underflow

While overflow causes abandonment, underflow does not. That seems like it could be an issue in some situations. Perhaps there should be a way to cause checked underflow.

## Preprocessor

C# offers a preprocessor which doesn't suffer from the issues of the C/C++ preprocessor. A preprocessor could be very useful in Adamant for conditional compilation of packages for different target platforms and controlling compilation. However, Adamant packages are meant to be cross-platform, and having different versions for different platforms could be bad. There is an idea to support different platforms through native packages. That may obviate some of the need for a preprocessor. Preprocessor directives would be introduced with "`##`" but otherwise function similar to the C# preprocessor. While the list below includes begin and end region directives, it should be carefully evaluated whether these should be added to Adamant.

* `##define`
* `##undefine`
* `##if`
* `##else`
* `##elseif`
* `##endif`
* `##error`
* `##warning`
* `##pragma`
* `##line`
* `##region`
* `##endregion`

## Documentation Comments

### Extended Markdown

Currently, documentation only supports a subset of [CommonMark](https://commonmark.org/). Ideally, it should support a much wider of range syntax, something like [Markua](http://markua.com/).

### Compile Code in Documentation Comments

Code in documentation comments should either be compiled, or have a way of causing it to be compiled.

## Expressions

### `else match` Expression

Allow a match to occur immediately after an else. Currently only if can occur there.

### `repeat {} while condition;` Loops

Rust doesn't have `do {} while condition;` loops. While they are rare, they do come up. Using `loop {} while <exp>;` to avoid introducing a new keyword was considered. However, someone reading the code wouldn't know to look for the while at the end or would have to check all loops to see if they ended with a while. Instead, the Swift style syntax was chosen. This makes it clear from the first keyword that this is a loop construct and not just some kind of action.

### Loop Else

Sometimes it is useful to execute some code if a loop is never run. This could be done with an else clause of the while and for loop.

```adamant
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
```

This can be useful for definite assignment. If the loop assigns a variable, it may be the case that the loop never runs and the variable may be unassigned. However, you can assign the variable in the else clause to a reasonable default so that the variable will definitely be assigned after the loop.

Note: this is different from the python style loop else construct which runs as long as the loop completed successfully.

Note: Alternatively, a different keyword or group of keywords could be used for loop else. Options include `otherwise`, `loop else`, `while else`, `for else`, or `if none`.

### Change Block Delimiters

The curly braces use up lines when declaring blocks. However, using only indention is problematic and doesn't allow for good auto formatting. Consider alternate block delimiters. Possibly use "`--`" to end a block. However, a block start is also needed to separate the condition of an if expression from the block unless parens are going to be required around the condition again. There is also a problem determining when a function signature ends and the body begins (consider requires clauses etc).

```adamant
public fn function()
    if(condition)
        statement1;
        statement2;
    --
--
```

```adamant
public fn function()    {
    if condition    {
        statement1;
        statement2; }   }
```

### `set` Expressions/Statements

Instead of allowing assignment expressions anywhere. Use a set expression "`set x = 5`". This makes a set as long as a redeclaration with "`let`". It allows the single equals sign "`=`" to be used as both assignment and comparison operations. Finally, it prevents any ambiguity for destructuring assignments "`set #(x, y) = function()`".

## Types

### Logarithmic Numbers

Support in the standard library for numbers that are represented in the [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system).

### Big Integers are Default

Make "`int`" and "`uint`" big integers with "`int32`" and "`uint32`" being added back. These big integers would pack into 64 bits most of the time and when they got bigger, change to 64 bit pointers. Given there is already overflow checks, the instructions for checking if a big integer is a pointer may not really be an issue. Also, on modern CPUs the performance issues are much more to do with waiting on other hardware. However, big integers that are a pointer could cause cash line misses and may require reference counting which could be costly.

### Have Both Default and Sized Integers

Currently, "`int`" and "`uint`" replace possible "`int32`" and "`uint32`" types. The language could have both as synonyms. This would allow for better conveying programmer intent. Programmers who weren't thinking about integer sizes could use "`int`" and "`uint`", programmers who consciously decided that 32 bits was the correct size for the situation could use "`int32`" and "`uint32`". However, this could lead to style splits with some devs claiming one should always use the explicit size variants. Furthermore, it would not be possible to produce a warning on their use, because it is only programmer intent.

### Relative Pointers

JAI will likely have relative pointers to pack pointers into smaller spaces that the 64-bit address space requires. Instead a relative pointer is smaller, but is a pointer relative to its own location. Thus to get the actual pointer, you must add the address of the relative pointer to the value of the regular pointer. Relative pointers would require the ability to specify their size. However, this requires that the programmer knows something about where things are allocated. For example, that all the values will be allocated in a single block of memory. Additionally, relative pointers introduce the possibility of overflow. Assigning a pointer into a relative pointer would cause overflow if the address pointed to is outside of what the relative pointer can point to. Early versions of JAI used "`*~s32 Node`" for a 32-bit relative pointer to a node. Notice a signed int is used. It may be possible to implement relative pointers in the standard library using structs.

### Reference Counted References

If reference counted references are built into the language then certain optimizations become possible. Whole program optimization could determine that no weak references are take to a given type and the weak count could be omitted. The graph of references between types could be used to determine which references could possibly for cycles. Then some form of cycle breaking might become efficient enough to use so that weak references aren't necessary. Of course, that could lead to issues where devs just use reference counting for everything and it becomes just a slow garbage collector.

### Move and Copy Reference Types

Copy constructors can be defined for reference types. However, would it be useful to have reference types that are implicit copy? Would it be useful to have reference types that are move by default. It seems that this might be useful for implementing something like a type state. Consider a socket object. This should be a reference type because it is large and may have multiple subtypes. However, it could go through three states represented by types (i.e. `AvailableSocket`, `OpenSocket`, `ClosedSocket`). There would be methods that took the one and returned the other. For example, the `close()` method would consume the socket and then return a `ClosedSocket`. However, this may not require move reference types. Indeed, one may not want move for most methods on socket. The close method could be declared `fn close(self$owned) -> ClosedSocket$owned`. It would then take ownership of self. That is a little strange because one can't use the `move` keyword with the self reference. The dot operator would have to do this implicitly.

## Named Parameters

Named parameters can be useful. I don't like how in C# every parameter could be potentially called as a named parameter. In Swift, there is syntax to control the name of a parameter independent of the name within the function. I think that makes sense since changing a parameter name is a breaking change. One could even allow multiple names for a parameter as a way of transitioning from an old name to a new name. The problem is that there is no good syntax for calling named parameters. The "`=`" would be ambiguous with assignment. The "`:`" would look like variable declarations and might conflict with current or future syntax. One person suggested using the keyword "`for`".

```adamant
function(5 for arg_2, 6 for arg_1);
```

## Development Aids

### Relaxed Borrow Checking

During development, the rules of borrow checking could be relaxed by actually using a garbage collector. It would be important to not let this get out of hand, but it might allow the running of functions that are being developed that still need to have the borrowing worked out.

## Generics

### Meta Functions

Functions with no runtime parameters that would always be evaluated at compile time. These would be declared by omitting the parens (i.e. `fn foo[n: size] -> Type`). They would be called without parens (i.e. `foo[45]`). Initially these seem like a cool idea. However there are some serious issues. They can't be generic in the types the operate on. For example, it wouldn't be possible to create a function that evaluated something as a const expression by passing it as a parameter (i.e. `constant[some_function()]`). That would not be possible because the function can't be generic in the type of its parameter. It might seem that meta functions would be useful for evaluating types, but pure functions can already be used for this. For example a pure function `fn tuple_of(params types: Array[Type]) -> Type` which constructs the tuple type for a list of types can already be written and (assuming it is pure) called in a type context. It would seem the only thing meta functions would allow one to do is create functions that appeared to be type constructors in the same way generic classes are. One question there is whether they would need to return a metatype for consistency with the way classes work. It isn't clear this would add much value when one can already use regular functions to construct types.

If one curried meta functions or allowed direct declaration, then maybe they could be generic. For example, `fn constant[T] -> [v: T] -> T` would accept a generic type parameter, then return a meta function that would take a value of that type and return it. It isn't clear whether the inference system could handle that. Alternatively, one could allow them to be directly declared as `fn constant[T][v: T] -> T`.

### Default To Inferred

Generic parameters can be given a default value. However, there are cases where one expects that users of a class will often want to pass one argument but have later arguments inferred. As an example, fixed size arrays `Unsafe_Array[n: size, T]` will need the size specified, but will often be able to infer the type parameter. Of course, this can already be fairly easily done with `Unsafe_Array[5, _]` at the use site. However, it could be useful to allow the class to declare that a parameter can be inferred if it is not used. One possible syntax for this would be `Unsafe_Array[n: size, T = _]`. Another possible syntax would be to allow curried or nested generics: `Unsafe_Array[n: size][T]`. Thus the first argument would be specified, but the second inferred. That syntax might imply that using the type would require double brackets as `Unsafe_Array[5][int]` which is odd.

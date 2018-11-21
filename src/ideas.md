# Appendix: Ideas

This appendix describes various ideas for features that could be added to the Adamant language in the future. Sometimes, it clarifies why a particular feature is currently not in the language.

Sections:

* [Operators](#operators)
* [Preprocessor](#preprocessor)
* [Documentation Comments](#documentation-comments)
* [Expressions](#expressions)
* [Types](#types)

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

Use "`xor`" as the logical exclusive or operator. It could have no precedence relative to the "`and`" and "`or`" operators. It is unlikely anyone would know the precedence of it. In fact, there may be disagreement about the correct precedence. *This has been omitted from the language for now to avoid imposing a precedence relative to the "`and`" and "`or`" operators before operator partial ordering is supported.

### Dot Product and Cross Product Operators

These seem like fairly standard useful operators. They may not be defined on the primitive types, but they could probably exist and be given precedence that mathematicians would expect.

### Use `<<` and `>>` as Operators

These character sequences are now available in the language and seem like they are evocative of operations like directing data etc. They could be useful.

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

Allow a match to occur immediatly after an else. Currently only if can occur there.

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

## Types

### Logarithmic Numbers

Support in the standard library for numbers that are represented in the [Logarithmic number system](https://en.wikipedia.org/wiki/Logarithmic_number_system).

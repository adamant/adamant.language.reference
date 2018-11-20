# Appendix: Ideas

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

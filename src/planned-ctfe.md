## Compile-time Function Execution

Compile-time function execution allows "arbitrary" functions to be executed at compile time. This can be useful for precalculating constants and initial values. It is also important to macro processing and language oriented programming. Generally, the thought is that generics would be reinterpreted as being the values that are known/computed at compile time. Thus generics would be similar to C++ templates but would be optimized into shared implementations when it made sense. This is consistent with the idea that all generic arguments must be known at compile time.

### Compile Time Limitations

Not all code would be executable at compile time. Instead, only code that had no side effects would be executable at compile time (no IO or mutating external state). This would be supported by the effect typing system so that any pure function could be evaluated at compile time. For this purpose, functions the developer explicitly marked as "pure" would be assumed to be pure and executable at compile time. There may also be limitations on the number of iterations loops were allowed to execute to avoid infinite loops during compile time.

### Custom Data

All data types should be computable at compile time and be able to be used either as constant values or as precomputed initial values. For example, it should be possible to precompute a hash table of values that was the initial value of a static variable but was then updated at runtime. This would allow user literals to be preconstructed from their string literal representation. It would also allow all user data types to be used as generic arguments.

### Compile Time Only Functions

There should be a way to indicate that certain pure functions should only be used at compile time and not at runtime. This would be a modifier or attribute that told the compiler the function couldn't be used at runtime and would ensure that the function was never emitted as compiled code. One use case for this would be code that performed hashing or encryption of security features. Another use case would be code for optimizing web assets (i.e. image optimizers).

### Const Expressions

There should be a way to clearly indicate that a given expression must be evaluated at compile time. Currently, the thought it that this would use the `const` keyword. It could be a simple expression as `const(`*expression*`)`. However, following the idea that all code in square brackets is evaluated/known at compile time, it could be `const[`*expression*`]`.

### Constant Propagation

While there are explicit constant expressions, any expression which the compiler determined was fully executable at compile time would be a candidate for constant propagation so that it could be evaluated and replaced with the resulting value.

### Type Functions

Compile time functions should be able to manipulate types using reflection in a way that interacts well with types. For example, it should be possible to use the result of a function as a type anywhere a type is expected. That could allow for complex behaviors like varying the return type of a function based on the generic parameters of that function in nontrivial ways. It isn't clear whether these would need to be meta functions as described in the next section or whether regular pure functions could be used.

### Meta Functions

Conceptually, type constructors are functions from types and values to types. Meta functions would remove the special classness of type constructors by allowing other functions over generic parameters. A meta function is a function with no runtime parameters that would always be evaluated at compile time. These would be declared by omitting the parentheses (i.e. `fn foo[n: size] -> Type`). They would be called without parentheses (i.e. `foo[45]`). There are some potential issues with meta functions though. They can't be generic in the types the operate on. For example, it wouldn't be possible to create a function that evaluated something as a const expression by passing it as a parameter (i.e. `constant[some_function()]`). That would not be possible because the function can't be generic in the type of its parameter. It might seem that meta functions would be useful for evaluating types, but pure functions would probably serve that purpose. For example a pure function `fn tuple_of(params types: Array[Type]) -> Type` which constructs the tuple type for a list of types can already be written and (assuming it is pure) called in a type context. It would seem the only thing meta functions would allow one to do is create functions that appeared to be type constructors in the same way generic classes are. One question there is whether they would need to return a metatype for consistency with the way classes work. It isn't clear this would add much value when one can already use regular functions to construct types.

If one curried meta functions or allowed direct declaration, then maybe they could be generic. For example, `fn constant[T] -> [v: T] -> T` would accept a generic type parameter, then return a meta function that would take a value of that type and return it. It isn't clear whether the inference system could handle that. Alternatively, one could allow them to be directly declared as `fn constant[T][v: T] -> T`.

The examples in the previous paragraph also point out that there might need to be function types for meta functions.

### Implementer's Notes

Executing code at compile time would most likely require some kind of interpreter or virtual machine so that code which contained pointer manipulation could be safely executed and so that data types containing pointers etc could be properly reified.

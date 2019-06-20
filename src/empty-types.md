## Empty Types

The empty types are those types for which there is no value of that type.

```grammar
empty_type
    : "never"
    | "void"
    ;
```

### `never` Type

The `never` type is the bottom type in Adamant. That is, it is a subtype of all other types. A function returning `never` or an expression of type `never` can't return a value. Instead it must either throw an exception, cause program abandonment, or never terminate. The never type is useful for functions which are known to never return. It is also the type of various expressions like `return`, `break`, `continue`, and `throw` which don't evaluate to a value. This allows for their use in boolean or coalescing expressions.

The `never` type can be useful in cases where a type is expected but can't occur. For example, a function expecting a "`Result[T, Error]`" type which is either a value of type "`T`" or an error of type "`Error`", could be passed a value of type "`Result[T, never]`" if an error is not possible in this circumstance. The value "`none`" used with [optional types](optional-types.md) has the type "`never?`".

### `void` Type

The `void` type functions similarly to `void` in languages like C, Java, and C#. However, those languages don't treat it as a proper type. In Adamant, `void` is a type that can be used as the argument to a generic parameter. Semantically, the void type behaves somewhat like a special unit type. However, rather than being a union type, it represents the absence of a type. Functions that return `void` don't return a value. Parameters of type `void` are dropped from the parameter list. It is illegal to directly use `void` in a number of ways a type normally can be simply because the result would be somewhat nonsensical. A variable or parameter can't be directly declared to have a void type. An expression of type void can't be assigned into anything. However, when void is used as the argument of a type parameter, it can cause variable and parameter declarations to be of type `void`. This is not an error. Parameters of type `void` are removed from the parameter list. Variables of type void are not allocated space. This can also create situations where an expression of a generic type is actually of type `void` and assigned into a variable of the same generic type. This is not an error. Effectively, the assignment is not performed.

Functions declared without a return arrow (`->`) and return type are defaulted to returning `void`. Explicitly declaring a function as returning void generates a warning.

For purposes of covariance and contravariance, "`void`" acts like a top type. For example, if a base class function returns "`void`" then it can be overridden with a function returning any type.

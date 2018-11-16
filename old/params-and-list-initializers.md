# Params and List Initializers

The `params` keyword allows for the creation of variadic functions. That is function that can take a variable number of arguments. It also allows for the invocation of variadic functions using list of arguments. Additionally, it can operate with tuples, not just lists.

## Params List

### Declaring a Variadic Function

A variadic function is declared by prefixing a parameter with the `params` keyword. That parameter should have a type that can be constructed with a list of values (see the next section). That is, the type of this argument is not limited to `Array[T]`. It may be borrow, borrow mut or owned. If the `params` parameter is the last parameter, this is always valid. If the parameter is not the last parameter, none of the following parameters can have default values.

```adamant
public fn example(params items: List[int]) -> void
{
    // ...
}
```

### Calling Variadic Functions

A variadic function can be called as if it were a normal function except that a variable number of arguments can be passed in place of the `params` parameter. This could be zero arguments.

```adamant
example(1, 2, 3, 4, 5);
```

### Construction of the List

When gathering the parameters into the collection type of the parameter, the compiler will generate code based on the available constructors and methods of the type. It will first look for a constructor taking `size, Raw_Array[T]` or `size, mut Raw_Array[T]`. If one of those exists, it will construct an raw array of the values and pass the number of values and the raw array to that constructor. Even if that constructor is `unsafe` (which it should be), no safety error will be generated if it is outside an unsafe context. If no such constructor is present, it will look for a constructor named `capacity` taking a single argument of `size`. If it exists, it will construct the collection using that constructor passing the number of values, then call `add()` repeatedly, passing each value. If no such constructor is present, it will look for a default constructor, use it and add the values. If none of these exists, an error will be generated.

### Calling Variadic Functions with a List

Sometimes you wish to pass a pre-existing list of values in place of the variable number of arguments. This can be done by prefixing the argument in place of the variadic arguments with the params keyword. Normal type compatibility and conversion rules apply between the passed collection argument and the expected collection type.

```adamant
let numbers = #[1, 2, 3, 4];
example(params numbers)
```

## Params Tuple

The `params` keyword can also be used with the tuple type. While this typically leads to functions with a fixed arity, variadic generics can be used to create variadic functions accepting a variable number of parameters of varying type. The `params` keyword works with the tuple type analogously to how it works with list. It can be used both for declaring a function and for invoking that function with an existing tuple. Additionally, it can be used with any function to take a tuple and expand it out so each element is passed as an argument. Thus the place where the params argument is passed is effectively replaced by an access to each element of the tuple for a number of arguments equal to the length of the tuple.

## Simple Types

The simple types are value types predefined by the Adamant language. They are identified by keywords. To declare an identifier with the same name as one of these, use an [escaped name](identifiers.md#escaped-identifiers). These keywords are fully distinct from the escaped name with the same letters. So the "`bool`" type and a "`\bool`" identifer are fully distinct and can never refer to each other.

```grammar
simple_type
    : numeric_type
    | "bool"
    | "never"
    | "void"
    | "type"
    | "metatype"
    ;

numeric_type
    : integral_type
    | floating_point_type
    ;
```

### Integral Types

The integral types provide signed and unsigned integers of various sizes. The "`int`" and "`uint`" types are each 32 bits.

```grammar
integral_type
    : "int8"
    | "byte"
    | "int16"
    | "uint16"
    | "int"
    | "uint32"
    | "int64"
    | "uint64"
    | "size"
    | "offset"
    ;
```

#### "`size`" and "`offset`" Types

* `size`
* `offset`

The bit sizes of these are system dependent. "`size`" is an unsigned number large enough to hold the maximum size of an array on the system. The size type is used to index into collections. "`offset`" is a signed number, with the same number of bits as "`size`", used to represent differences between array indexes and pointers.

### Floating Point Types

The types "`float32`" and "`float`" are floating-point types represented as IEEE 754 32-bit and 64-bit values.

```grammar
floating_point_type
    : "float32"
    | "float"
    ;
```

Floating point operations never cause abandonment. In exceptional situations, they produce zero, positive or negative infinity or not a number (NaN). Floating point operations may be performed with higher precision than the result type of the operation. Some hardware architectures support higher precision operations and implicitly perform all floating-point operations using this higher precision type. Only with a performance impact can they be made to perform floating point operations with lower precision. This rarely has an impact on the results of a numeric calculation. However in expressions like "`x * y / z`" where the multiplication produces results outside the floating point range and the division brings the temporary result back in range, the higher precision may cause a finite result to be produced rather than infinity.

### "`bool`" Type

The "`bool`" type represents boolean values. The possible values of a boolean are "`true`" and "`false`". The boolean logical operators "`and`" and "`or`" operate on boolean types. The condition of an "`if`" expression and "`while`" loop must evaluate to booleans.

No standard conversions exist between "`bool`" and other types. The bool type is distinct and separate from the integral types. A bool value cannot be used in place of an integral value, and vice versa.

### `never` Type

The type "`never`" is a type with no values. It is used to indicate that a function never returns by normal means (it may still throw an exception). It can also be useful in cases where a type is expected but can't occur. For example, a function expecting a "`Result[T, Error]`" type which is either a value of type "`T`" or an error of type "`Error`", could be passed a value of type "`Result[T, never]`" if an error is not possible in this circumstance. The type "`never`" is a subtype of all types. Thus an expression with type "`never`" is assignment compatible with all types. The "`none`" value used with [optional types](optional-types.md) has the type "`never?`".

### `void` Type

The type "`void`" is a type used to indicate that something logically has no value. It is similar to the unit type in functional programming. However, the Adamant language enforces that an expression of type "`void`" can't be assigned into anything. However, when a type parameter is passed the "`void`" type this can create assignments from "`void`" to "`void`" which are not an error. Functions may not have parameters of type "`void`". However, if "`void`" is passed as a type parameter used as the type of a function parameter, that parameter is dropped from the parameter list.

For purposes of covariance and contravariance, "`void`" acts like a top type. For example, if a base class function returns "`void`" then it can be overridden with a function returning any type.

### `type` Type

The type "`type`" is the type of all objects that represent types during reflection and in generics. The default type of a generic parameter is "`type`" (thus "`foo[T]`" and "`foo[T: type]` are equivalent). Any generic parameter with this type is referred to as a "type parameter". Note that "`type`" is a value type. If necessary, it will use reference counting behind the scenes. This simplifies issues of lifetimes around type parameters.

### `metatype` type

The type "`metatype`" is a subtype of "`type`". Objects representing the type of classes and structs have a type that is a subtype of "`metatype`". When calling associated functions as "`Example.function()`", the expression "`Example`" has a subtype of "`metatype`". Metatypes describe the associated functions and constructors on the type object.

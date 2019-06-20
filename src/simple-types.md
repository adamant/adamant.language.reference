## Simple Types

The simple types are value types predefined by the Adamant language. They are identified by keywords. To declare an identifier with the same name as one of these, use an [escaped name](identifiers.md#escaped-identifiers). These keywords are fully distinct from the escaped name with the same letters. So the "`bool`" type and a "`\bool`" identifer are fully distinct and can never refer to each other.

```grammar
simple_type
    : numeric_type
    | "bool"
    ;

numeric_type
    : integer_type
    | floating_point_type
    ;
```

### Integer Types

The integer types provide signed and unsigned integers of various sizes. The "`int`" and "`uint`" types are each 32 bits.

```grammar
integer_type
    : "int8"
    | "byte"
    | "int16"
    | "uint16"
    | "int"
    | "uint"
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

The types "`float32`" and "`float`" are floating-point types represented as IEEE 754 binary32 and binary64 values.

```grammar
floating_point_type
    : "float32"
    | "float"
    ;
```

Floating point operations never cause abandonment. In exceptional situations, they produce zero, positive or negative infinity or not a number (NaN). Floating point operations may be performed with higher precision than the result type of the operation. Some hardware architectures support higher precision operations and implicitly perform all floating-point operations using this higher precision type. Only with a performance impact can they be made to perform floating point operations with lower precision. This rarely has an impact on the results of a numeric calculation. However in expressions like "`x * y / z`" where the multiplication produces results outside the floating point range and the division brings the temporary result back in range, the higher precision may cause a finite result to be produced rather than infinity.

### "`bool`" Type

The "`bool`" type represents boolean values. The possible values of a boolean are "`true`" and "`false`". The boolean logical operators "`and`" and "`or`" operate on boolean types. The condition of an "`if`" expression and "`while`" loop must evaluate to booleans.

No standard conversions exist between "`bool`" and other types. The bool type is distinct and separate from the integer types. A bool value cannot be used in place of an integer value, and vice versa.

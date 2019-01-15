## Operator Overloading

An *operator overload* is a method that defines the meaning of an operator expression when applied to instances of the class. Operators are declared similarly to functions using operator declarations. These declarations use `_` as a placeholder to indicate the positions of the operands. This distinguishes nullary, unary prefix, unary postfix, and binary operators.

Operator overloads can be generic functions. To make one generic place the generic parameters after the `operator` keyword. However, since it isn't possible to pass generic parameters when using operators, they are limited to cases where the generic parameters can be inferred.

```grammar
operator_declaration
    : access_modifier attributes operator_name parameters function_body
    ;

operator_name
    : "operator" generic_parameters overloadable_operator
    | "implicit" "operator" literal_operator
    ;

overloadable_operator
    : overloadable_nullary_operator
    | overloadable_unary_operator
    | "_" overloadable_bindary_operator "_"
    ;

overloadable_nullary_operator
    : ".."
    | "<.."
    | "..<"
    | "<..<"
    ;

overloadable_unary_operator
    : "+" "_"
    | "-" "_"
    | "^" "_"
    | ".." "_"
    | "<.." "_"
    | "..<" "_"
    | "<..<" "_"
    | "_" ".."
    | "_" "<.."
    | "_" "..<"
    | "_" "<..<"
    ;

overloadable_binary_operator
    : "+"
    | "-"
    | "*"
    | "/"
    | ".."
    | "<.."
    | "..<"
    | "<..<"
    | "+="
    | "-="
    | "*="
    | "/="
    ;

literal_operator
    : "'_'"
    | <String literal for single underscore (i.e. "_" including the double quotes)>
    ;
```

### Overloadable Operators

Most overloadable operators act as simple methods. However, some operators require special handling or have special effects. This table summarizes which operators are overloadable and if they require special handling.

| Operators                              | Overloadability                                                              |
| -------------------------------------- | ---------------------------------------------------------------------------- |
| `+` , `-` , `^`                        | These unary operators can be overloaded                                      |
| `@` , `=>`, `not`                      | These unary operators can't be overloaded                                    |
| `+` , `-` , `*` , `/`                  | The arithmetic operators can be overloaded                                   |
| `+=` , `-=` , `*=` , `/=`              | The arithmetic assignment operators can be overloaded for greater efficiency |
| `..` , `<..` , `..<` , `<..<`          | The range operators can each be overloaded independently                     |
| `::` , `^.` , `<:` , `\|` , `&`        | These binary operators can't be overloaded                                   |
| `as` , `as!` , `as?`                   | The conversion operators are not overloadable                                |
| `==` , `=/=` , `<` , `>` , `<=` , `>=` | The comparison operators can be overloaded in pairs                          |
| `and` , `or`, `?.`, `??` , `.`         | Support for overloading these operators is planned for future releases       |
| `=` , `->` , `f(x)`                    | These operators can't be overloaded                                          |

### Example Types

These types are used for the examples in the following sections.

```adamant
public copy struct complex
{
    public let real: float;
    public let imaginary: float;

    public init(.real, .imaginary) { }
}

public copy struct fuzzy_bool
{
    public let value: float;

    public init(.value)
        requires value >= 0 and value <=1
    {
    }
}
```

### Unary Operators

Four unary operators are overloadable. The `+` and `-` operators are straightforward overloads. Note that both are prefix operators and their declarations have a single operand placeholder after the operator.

```adamant
// In `complex`
public operator +_(ref self) -> complex
{
    // return a copy, unchanged
    return self;
}

public operator -_(ref self) -> complex
{
    return complex(-real, -imaginary);
}
```

#### The Dereference Operator

It is not possible to overload the dereference and access operator `^.` directly. However, when the dereference operator `^` is overloaded it will be used to evaluate the `^.` operator by first calling the overloaded dereference operator and then applying standard member access to the resulting value. The dereference operator is overloadable to allow for value types that act like pointers. The dereference operator is a prefix operator, so its declaration has a single operand placeholder after the operator.

```adamant
public move struct unique_pointer[T]
    where T: struct
{
    private let ptr: @T;

    public safe operator ^_(ref self) -> ref T
    {
        return unsafe(ref *ptr);
    }
}
```

### Arithmetic Operators

The binary arithmetic operators `+` , `-` , `*` , and `/` can be overloaded.

```adamant
// In `complex`
public operator _+_(ref self, other: ref complex) -> complex
{
    return complex(real + other.real, imaginary + other.imaginary);
}
```

In order to support operators whose left hand side is a primitive type. In binary operators, the self parameter may be the second parameter.

```adamant
// In `complex`
public operator _+_(ref self, other: float) -> complex
{
    return complex(real + other, imaginary);
}

// In `complex`
public operator _+_(other: float, ref self) -> complex
{
    return complex(other + real, imaginary);
}
```

### Assignment Operators

These operators will be automatically generated if the corresponding binary operator is overloaded. However, they can be directly overloaded for greater efficiency, different behavior or to mark them as uncallable.

### Range Operators

The range operators can each be overloaded independently. However, it is recommend that the exclusive range operators be overloaded as a set. The inclusive range operators can be used as unary operators for open ended ranges. Furthermore they can be used as nullary operators for open ranges. The nullary, unary prefix, unary suffix and binary forms of these operators are distinguished by their operand placeholders.

### Comparison Operators

The comparison operators can be overloaded like other binary operators. However, they must overloaded in pairs. When one of the operators in pair is overloaded, the other one in the pair must be overloaded to.

| Operator | Pairs |
| -------- | ----- |
| `==`     | `=/=` |
| `>`      | `<`   |
| `>=`     | `<=`  |

For operators that have multiple syntax forms, these are all treated as equivalent. Note that the comparison operators cannot be overloaded for lifetime comparisons.

Note: The `obsolete` attribute can be used to mark a member of the pair as uncallable if it is explicitly desired not to implement the other member of the pair.

### Literal Operators

The literal operators `'_'` and `"_"` allow for the construction of values from user defined and string literals respectively. As described in the literals section, the resulting object must be read only and have the static lifetime. This operator must be an implicit pure function. For simplicity, no spaces are allowed around the underscore between the delimiters.

```adamant
public struct Example
{
    public let value: string;

    public init(.value) { }

    public implicit operator '_'(value: string) -> Example$forever
    {
        return Example(value);
    }

    public implicit operator "_"(count: size, bytes: @byte) -> Example$forever
    {
        return Example(count, bytes);
    }
}
```

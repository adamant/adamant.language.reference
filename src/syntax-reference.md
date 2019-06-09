# Appendix: Syntax Reference

A brief statement of the syntax for easy reference.

## Identifiers

| Syntax            | Identifier Kind                                                       |
| ----------------- | --------------------------------------------------------------------- |
| `Name`            | Identifer                                                             |
| `\class`          | Escaped Identifier (keywords, contextual keywords and reserved words) |
| `\"Hello World!"` | String Identifier                                                     |

## Literals

| Syntax                   | Meaning                     |
| ------------------------ | --------------------------- |
| `true`, `false`          | Boolean Literals            |
| `"Hello!"`               | String Literal              |
| `""" Multiple lines """` | Multiline String Literal    |
| `" Hello \(name)"`       | Interpolated String Literal |

## Operators

| Operator  | Meaning                        |
| --------- | ------------------------------ |
| `x + y`   | Addition                       |
| `x - y`   | Subtraction                    |
| `x * y`   | Multiplication                 |
| `x / y`   | Division                       |
| `^x`      | Dereference                    |
| `x^.y`    | Deference and Access Member    |
| `@x`      | Address Of (i.e. pointer to)   |
| `x??y`    | Coalesce Operator}             |
| `x?.y`    | Conditional Access Operator    |
| `x and y` | Logical And (Short Circuiting) |
| `x or y`  | Logical Or (Short Circuiting)  |
| `x..y`    | Inclusive Range                |
| `x..<y`   | Range Exclusive of End         |
| `x<..y`   | Range Exclusive of Start       |
| `x<..<y`  | Exclusive Range                |

## Types

| Syntax                         | Meaning                                            |
| ------------------------------ | -------------------------------------------------- |
| `Type`                         | Type Name                                          |
| `Type[T, R]`                   | Generic parameters and compile time code execution |
| `@T`                           | Non-null Pointer to `T`    (i.e. Address of `T`)   |
| `@T?`                          | Nullable Pointer to `T`                            |
| `@void`, `@mut void`           | Void Pointers?                                     |
| `(Type1, Type2) -> ReturnType` | Function Type                                      |

## Expressions

| Syntax                | Meaning                                 |
| --------------------- | --------------------------------------- |
| `#[x, y]`             | List/Array Initializer                  |
| `#{x, y}`             | Set Initializer                         |
| `#(x, y)`             | Tuple Initializer                       |
| `fn(x) { ... }`       | Anonymous Function                      |
| `fn(x) -> Type {...}` | Anonymous Function with Return Type     |
| `fn(x) => expression` | Anonymous Function with Expression Body |

## Attributes

| Syntax                 | Meaning |
| ---------------------- | ------- |
| `#Attribute public...` |         |

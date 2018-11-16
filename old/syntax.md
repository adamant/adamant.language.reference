# Syntax Reference

This section gives a brief statement of the syntax for easy reference.

## Identifiers

| Syntax            | Identifier Kind    |
| ----------------- | ------------------ |
| `Name`            | Identifer          |
| `\class`          | Escaped Identifier |
| `\"Hello World!"` | String Identifier  |

## Literals

| Syntax                   | Meaning                     |
| ------------------------ | --------------------------- |
| `true`, `false`          | Boolean Literals            |
| `"Hello!"`               | String Literal              |
| `""" Multiple lines """` | Multiline String Literal    |
| `" Hello \(name)`        | Interpolated String Literal |

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
| `x xor y` | Logical Xor                    |
| `x..y`    | Inclusive Range                |
| `x..<y`   | Range Exclusive of End         |
| `x<..y`   | Range Exclusive of Start       |
| `x<..<y`  | Exclusive Range                |

## Keywords

| Keyword     | Meaning |
| ----------- | ------- |
| `if`        |         |
| `else`      |
| `match`     |
| `let`       |         |
| `var`       |         |
| `ref`       |
| `new`       |
| `init`      |
| `delete`    |
| `mut`       |
| `never`     |
| `void`      |
| `bool`      |
| `true`      |
| `false`     |
| `string`    |
| `int`       |
| `uint`      |
| `float`     |
| `decimal`   |
| `byte`      |
| `size`      |
| `offset`    |
| `none`      |
| `public`    |
| `private`   |
| `protected` |
| `internal`  |
| `safe`      |
| `unsafe`    |
| `struct`    |
| `class`     |
| `implicit`  |
| `copy`      |
| `move`      |
| `operator`  |
| `loop`      |
| `while`     |
| `foreach`   |
| `in`        |
| `break`     |
| `next`      |
| `self`      |
| `base`      |
| `Self`      |
| `sizeof`    |
| `no`        |
| `throw`     |
| `extend`    |
| `partial`   |
| `replace`   |
| `alias`     |
| `enum`      |
| `return`    |
| `yield`     |
| `params`    |
| `external`  |
| `unchecked` |

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

## Preprocessor

| Syntax       | Meaning |
| ------------ | ------- |
| `##if`       |         |
| `##define X` |         |

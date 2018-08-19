# Syntax Reference

This section gives a brief statement of the syntax for easy reference.

## Identifiers

| Syntax            | Identifier Kind    |
| ----------------- | ------------------ |
| `Name`            | Identifer          |
| `\class`          | Escaped Identifier |
| `\"Hello World!"` | String Identifier  |

## Literals

| Syntax         | Meaning          |
| -------------- | ---------------- |
| `true` `false` | Boolean Literals |

## Operators

| Operator | Meaning                     |
| -------- | --------------------------- |
| `x + y`  | Addition                    |
| `^x`     | Dereference                 |
| `x^.y`   | Deference and Access Member |

## Types

| Syntax                         | Meaning                                            |
| ------------------------------ | -------------------------------------------------- |
| `Type`                         | Type Name                                          |
| `Type[T, R]`                   | Generic parameters and compile time code execution |
| `@T`                           | Non-null Pointer to `T`    (i.e. Address of `T`)   |
| `@T?`                          | Nullable Pointer to `T`                            |
| `@void` `@mut void`            | Void Pointers?                                      |
| `(Type1, Type2) -> ReturnType` | Function Type                                      |


## Expressions

| Syntax                | Meaning                                 |
| --------------------- | --------------------------------------- |
| `#[x, y]`             | List/Array Initializer                  |
| `#{x, y}`             | Set Initializer                         |
| `#(x, y)`             | Tuple Initializer                       |
| `fn(x) {}`            | Anonymous Function                      |
| `fn(x) -> Type {...}` | Anonymous Function with Return Type     |
| `fn(x) => expression` | Anonymous Function with Expression Body |

## Attributes

| Syntax                 | Meaning |
| ---------------------- | ------- |
| `#Attribute public...` |         |

## Preprocessor

| Syntax | Meaning |
| ------ | ------- |
| `##if` |         |

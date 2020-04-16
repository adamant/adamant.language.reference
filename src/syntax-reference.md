# Appendix: Syntax Reference

A brief statement of the syntax for easy reference.

## Identifiers

| Syntax            | Identifier Kind                                                       |
| ----------------- | --------------------------------------------------------------------- |
| `Name`            | Identifer                                                             |
| `\class`          | Escaped Identifier (keywords, contextual keywords and reserved words) |
| `\"Hello World!"` | String Identifier                                                     |

## Comments

| Syntax             | Meaning               |
| ------------------ | --------------------- |
| `// something`     | Line Comment          |
| `/* something */`  | Multiline Comment     |
| `/// Markdown text | Documentation Comment |

## Literals

| Syntax                             | Meaning                     |
| ---------------------------------- | --------------------------- |
| `true`, `false`                    | Boolean Literals            |
| `-10`, `100 000`                   | Integer literals            |
| `0xAF 6D`                          | Hexadecimal Literals        |
| `0b1010 0101`                      | Binary Literals             |
| `-1.2`, `0.324`, `1561e-46`        | Real Literals               |
| `'c'`, `'2018-09-28'`              | User Literals               |
| `"Hello!"`                         | String Literal              |
| `" Hello \(name)"`                 | Interpolated String Literal |
| `#"He said "\Whoa\""#`             | Raw String Literals         |
| `"""`<br>`Multiple lines`<br>`"""` | Multiline String Literal    |

## Operators

| Operator           | Meaning                        |
| ------------------ | ------------------------------ |
| `x + y` †          | Addition                       |
| `x - y` †          | Subtraction                    |
| `x * y` †          | Multiplication                 |
| `x / y` †          | Division                       |
| `x.y`              | Member Access                  |
| `^x`               | Dereference                    |
| `x^.y`             | Deference and Access Member    |
| `@x`               | Address Of (i.e. pointer to)   |
| `x??y`             | Coalesce Operator             |
| `x?.y`             | Conditional Access Operator    |
| `x and y`          | Logical And (Short Circuiting) |
| `x or y`           | Logical Or (Short Circuiting)  |
| `..`               | Infinite Range                 |
| `x..y`             | Inclusive Range                |
| `x..`              | Inclusive Range to Infinity    |
| `..y`              | Inclusive Range from Infinity  |
| `x..<y` ‡          | Range Exclusive of End         |
| `x<..y` ‡          | Range Exclusive of Start       |
| `x<..<y` ‡         | Exclusive Range                |
| `p::.n`            | Package Qualifier              |
| `::.n`             | Global Qualifier               |
| `x = y`            | Assignment                     |
| `x == y`           | Equal                          |
| `x ≠ y`, `x =/= y` | Not Equal                      |
| `x < y`            | Less Than                      |
| `x ≤ y`, `x <= y`  | Less Than or Equal             |
| `x > y`            | Greater Than                   |
| `x ≥ y`, `x >= y`  | Greater Than or Equal          |
| `=> x`             | Result of Block or Expression  |
| `x and y`          | Logical And (short circuiting) |
| `x or y`           | Logical Or (short circuiting)  |
| `not x`            | Logical Not                    |
| `T1 \| T2`          | Sum Type                       |
| `T1 & T2`          | Intersection Type              |
| `T1 <: T2`         | Subtype                        |

† Can be combined with assignment (i.e. `+=`) \
‡ All ranges can be infinite on either or both sides as shown for inclusive ranges

## Types

| Syntax                         | Meaning                                            |
| ------------------------------ | -------------------------------------------------- |
| `Type`                         | Type Name                                          |
| `Type[T, R]`                   | Generic parameters and compile time code execution |
| `@T`                           | Non-null Pointer to `T`    (i.e. Address of `T`)   |
| `@T?`                          | Nullable Pointer to `T`                            |
| `@void`, `@mut void`           | Void Pointers?                                     |
| `(Type1, Type2) -> ReturnType` | Function Type                                      |

## Statements

| Syntax                      | Meaning              |
| --------------------------- | -------------------- |
| *expression*`;`             | Expression Statement |
| `if c { } else { }`         | If Statement         |
| `match v { p1 { }, p2 {} }` | Match Statement      |
| `loop { }`                  | Loop                 |
| `while c { }`               | While Loop           |
| `foreach v in s { }`        | Foreach Loop         |

## Expressions

| Syntax                           | Meaning                                 |
| -------------------------------- | --------------------------------------- |
| `break`, `break v`, `break $l v` | Break Loop Expression                   |
| `continue`, `next $l`            | Next Loop Iteration Expression          |
| `#[x, y]`                        | List/Array Initializer                  |
| `#{x, y}`                        | Set Initializer                         |
| `#(x, y)`                        | Tuple Initializer                       |
| `fn(x) { ... }`                  | Anonymous Function                      |
| `fn(x) -> Type {...}`            | Anonymous Function with Return Type     |
| `fn(x) => expression`            | Anonymous Function with Expression Body |

## Attributes

| Syntax                 | Meaning |
| ---------------------- | ------- |
| `#Attribute public...` |         |

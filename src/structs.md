# Structs

Structs are similar to classes. They contain field and function members. Unlike classes, structs are value types and by default are allocated on the stack instead of the heap. A variable with a struct type directly contains the data of the struct. A variable of a class type contains a reference to the object which contains the data.

There are three main kinds of structs. This kind determines the semantics of assigning values of that type. Additionally, there are enum structs documented in their own section.

| Struct Type              | Declaration Syntax | Semantics                                                              |
| ------------------------ | ------------------ | ---------------------------------------------------------------------- |
| Move Structs             | `move struct`      | Struct values "move" when assigned so that they are no longer be used. |
| Copy Structs             | `copy struct`      | Struct values are implicitly copied when assigned.                     |
| Pseudo-reference Structs | `ref struct`       | Appear to behave like references.                                      |

The kind of struct determines the default assignment behavior, but it is often possible to explicitly cause the other behavior.

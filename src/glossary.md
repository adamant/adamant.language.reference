## Appendix: Glossary

Reference Type
: Any type that is implicitly passed by reference. All types declared using the `class` keyword are reference types.

Value Type
: Any type that is implicitly passed by value. All types declared using the `struct` keyword are value types.

Copy Type
: A value type that has an implicit copy constructor so that when it is passed by value, a copy is passed rather than moving the value. Any type declared using the `struct` keyword that also has a copy constructor declared `implicit` is a copy type.

Move Type
: A value type that does not have an implicit copy constructor so that when it is passed by value, the value is moved out of the original variable. Any type declared using the `struct` keyword that does *not* have a copy constructor declared `implicit` is a move type.

Object Type
: A type of an object. Types declared with `class` and `struct` are object types. Types that are not object types include function types and ref types (note: ref types are different than reference types).

Ref Type
: A ref type is the type of a variable reference. That is a reference to a variable rather than an object.

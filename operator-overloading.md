# Operator Overloading

Operators can be overloaded using a syntax very similar to C#.  Operators are not tied to interfaces the way they are in Rust because that would imply that they have universal semantics.  That is, that every use of the operator is meant to have the same semantics.  However, just like method names, the same name can have two different meanings in different contexts.
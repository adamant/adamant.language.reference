## Reserved Words

Reserved words are identifier like character sequences that are reserved for possible future use as keywords. They can't be used as identifiers except by escaping them. Some reserved words are specifically reserved as types because they follow the pattern of existing types.

Use of reserved words is a fatal error. However, the compiler should treat all reserved words as identifiers for the purposes of further analysis. There is one exception to this. The reserved word `continue` is treated as a synonym for `next`, but will generate a nonfatal error.

The grammar below notes some possible uses of the reserved words or justifications for reserving them. They may be used for these purposes or others, or may eventually be removed from the reserved word list and be made available for use as identifiers.

```grammar
reserved_word
    : reserved_type
    | "alias"              // Planned Type Alias Feature                                                 |
    | "case"               // Useful for switch like constructs
    | "cast"               // Casting
    | "checked"            // Checked Operations
    | "const_cast"         // Casting
    | "continue"           // Useful for control flow
    | "default"            // Useful for switch like constructs and default values
    | "defer"              // Swift style "`defer`" statements
    | "do"                 // "`do while`" loop or Swift style "`do`" block
    | "dynamic_cast"       // Casting
    | "extend"             // Extensions
    | "extension"          // Extensions
    | "fallthrough"        // Useful for switch like constructs
    | "for"                // Common Keyword
    | "from"               // Common Keyword
    | "guard"              // Swift style guard statements
    | "internal"           // Common Keyword
    | "null"               // Null value for pointers
    | "otherwise"          // Loop Else
    | "package"            // Qualify names with the current package (i.e. `package::.name`)
    | "partial"            // Partial Classes
    | "private"            // Common Keyword
    | "reinterpret_cast"   // Casting
    | "repeat"             // Swift style "`repeat {} while condition;`" loop
    | "replace"            // Partial Classes
    | "select"             // C# style query
    | "sizeof" | "size_of" // Size of Operator
    | "switch"             // Useful for switch like constructs
    | "symmetric"          // Symmetric operators
    | "transmute"          // Reinterpret Cast
    | "then"               // Python style loop else
    | "type"               // Type aliases and declarations
    | "unchecked"          // Unchecked Operations
    | "unless"             // Ruby style `if not` statement or `unless break` for Python style loop else
    | "when"               // C# style exception filters
    | "xor"                // Logical exclusive or operator
    | "yield"              // Generators
    ;
```

Note: The "`then`" keyword would allow for "`while condition { } then { } else { }`". The "else" block would be executed if the condition is false the first time it is evaluated. The "then" block would be executed if control flow left the end of the loop body (i.e. the loop body executed at least once and wasn't exited with a "`break`" expression). It is unclear of the semantics should be that a "`then`" without an "`else`" would execute even if the loop never did. (see also `unless break`)

### Reserved Types

Some identifiers are reserved for use as type names. They generally follow the pattern of existing types. However, the `fixed`, `ufixed`, and `real` types include a period which no identifier does. Note that even patterns whether the digit portion starts with zero are reserved.

```grammar
reserved_type
    : "int" [0-9]+ - keyword   // unused integer types
    | "uint" [0-9]+ - keyword  // unused unsigned integer types
    | "float" [0-9]+ - keyword // unused floating point types
    | "fixed"
    | "fixed" [0-9]+ "." [0-9]+
    | "ufixed"
    | "ufixed" [0-9]+ "." [0-9]+
    | "decimal"
    | "decimal" [0-9]+
    | "real"
    | "real." [0-9]+
    ;
```

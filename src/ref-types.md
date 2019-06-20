## Variable Reference Types

Variable reference types are the types of references to variables. Note that while they are reference types in the sense that they reference a value, they are not classed as reference types in Adamant. The reason for this is that they differ from the reference types in important ways. A variable reference never "owns" its value as the value is always owed by the variable being referenced. Furthermore, a variable reference does not need to be a fat pointer because no vtable is necessary. Lastly, it is possible to chain variable references to produce references to references.

```grammar
variable_reference_type
    : "ref" type
    | "ref" "var" type
    ;
```

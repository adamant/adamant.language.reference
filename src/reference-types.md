## Reference Types

```grammar
reference_type
    : type_type
    | object_type
    | function_type
    | "Any"
    | variable_reference_type
    ;
```

### `Type` and `Metatype`

```grammar
type_type
    : `Type`
    | `Metatype`
    ;
```

The type "`Type`" is the type of all objects that represent types during reflection and in generics. The default type of a generic parameter is "`Type`" (thus "`foo[T]`" and "`foo[T: Type]` are equivalent). Any generic parameter with this type is referred to as a "type parameter". Note that "`Type`" is a reference type. This was necessary to allow metatypes to be subtypes of it.

The type "`Metatype`" is a subtype of "`Type`". Objects representing the type of classes and structs have a type that is a subtype of "`Metatype`". When calling associated functions as "`Example.function()`", the expression "`Example`" has a type that is a subtype of "`Metatype`". Metatypes describe the associated functions and constructors on the type object. The meta type of a type can be accessed using the "`Type`" property, for example "`Example.Type`".

These types can be very confusing. For some, an example clarifies the relationship.

```adamant
let example: Example = new Example();
let exampleType: Example.Type = Example;
let meta: Metatype = exampleType; // Example.Type <: Metatype
let type: Type = meta; // Metatype <: Type
```

### `Any` Type

All object types, function types and "`Type`" are a subtype of the "`Any`" type. Note that variable references are not included in this.

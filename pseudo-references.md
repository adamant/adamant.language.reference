# Pseudo References

You've learned all about the ownership system in Adamant. About reference types, value types, variable references and lifetimes, but some types you use on a regular basis may not seem like they fit in that list. It is possible using the features of Adamant to create types that behave differently then one might expect. An important case of this are *pseudo references*. These are value types that appear as if they are actually reference types. Examples include `string`, `Array[T]`, and `Counted_Ref[T]`. Let's explore how pseudo references work.

## Declaring

```adamant
public struct Test$t
{
    private let x: string$t;

    // This delete will be called when the struct has the lifetime $owned when it goes out of scope
    public delete()
        // if the where clause is used, this should maybe be where $t == $owned
    {
        // ...
    }

    // This delete will be called when the struct does not have the lifetime $owned when it goes out of scope
    // The logic here is that regular delete has a special form of ownership, while this delete just gets a reference
    // However that is confusing. A better syntax is really needed.
    public delete(ref mut self)
        where $t =/= $owned // idea for another syntax
    {
        // ...
    }
}
```

## In the Standard Library

### `string`

The predefined string type is defined as a `struct` for efficiency. At the same time, passing around a large string doesn't constantly copy all that data. Furthermore, if you've used methods like `substring()` you know that strings can have lifetimes as if they were references.

### `Counted_Ref[T]`

The `Counted_Ref[T]` defined in the standard library is a true pseudo reference. It is designed to behave as much life a reference to an object of type `T` as possible.

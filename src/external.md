# External Declarations

The `external` keyword declares blocks of functions, structs and global variables that are implemented externally. Use compiler attributes and compiler settings to control this.

## External Structs

Structs declared in external block follow the layout rules of the target language.

## External Function Example

All parameters and returns from C external functions must be one of:

* A simple numeric type (i.e. `int`, `float` etc.)
* An external struct
* A pointer to one of the above

```adamant
#link("libc") // Less generic attribute name
external // by default, changes to 'C' ABI
{
    public fn function() -> int;
}
```

## Exporting Example

Exporting allows functions to be called from other languages.

```adamant
#export // tells it to make this function visible externally, and not mangle the name, by default, changes to 'C' ABI
publish fn something() -> void
{
    // implementation
}

#export("NewName") // export as the given name
#calling_convention(.Fast) // should take an enum value, can also be applied to external block
publish fn something2() -> void
{
    // implementation
}
```

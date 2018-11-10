# Namespaces and Using Directives

## Namespaces

All declarations in an Adamant program are in some namespace. This may be the global namespace which is an unnamed namespace that forms the root of the namespace hierarchy.

The namespace of the declarations in a code file are automatically determined by the files location in the directory structure. The package determines a root directory for the source. Each directory or subdirectory establishes a new namespace nested inside the namespace of the containing directory. All declarations in code files inside that directory are in that namespace.

The package can specify a root namespace. If a root namespace is specified, then the package root directory does not represent the global namespace, but the specified root namespace.

Further namespaces can be created both by directories and by a namespace declaration.

```adamant
namespace something.more
{
    // declarations in the something.more namespace
}
```

Like all other declarations, namespace declarations are nested inside the namespace established by the location of the source file. However, namespace declarations can be removed from this by prefixing their name with `::`.

```adamant
namespace ::anotherRoot
{
    // declarations in the ::anotherRoot namespace
}
```

## Declaration Visibility

Namespaces are searched from inside out for a name. If a matching declaration is found in the namespace, then resolution stops. Otherwise, declarations in nested namespaces visible from the current code are searched. All declarations in nested namespaces are considered together so that declarations in two different sub-namespaces could cause ambiguity. Then the containing namespace of the namespace currently being searched is checked. This means that all declarations will be visible in the global namespace. If a declaration with the same name whose type doesn't match the usage is found, name resolution continues to search.

## Using Directives

Using directives can appear at the top of a source code file before any other declarations, or inside a namespace declaration before any other declarations. As namespaces are searched inside out, using directives are searched before the containing namespace. If a using directive references a namespace, it brings into scope only declarations directly in the namespace, not in namespaces nested inside it. A using directive can also name a type declaration, in which case all associated functions of the type are brought into scope.

```adamant
namespace example1
{
    public class List[T] {} // A declaration that conflicts with the list in the standard library
    public class Foo[T]
    {
        public fn Bar() -> T
        {
            // ...
        }
    }
}

namespace example2
{
    using system.collections; // Without this, references to List[T] would search up to the global namespace and be ambiguous.
    using example1.Foo[int]; // This makes `Bar() -> int` visible.
}
```

Using directives pull in names for the given namespace from all packages. If needed, the name can be disambiguated using a package qualifier.

```adamant
using ::example; // Only members of the example namespace in the current package.
using system.collections::system.collections.specialized; // Only members of the system.collections.specialized namespace in the `system.collections` package.
```

## Summary of Name Resolution

Name resolution searches until it finds a matching declaration. If a given scope has multiple matching declarations, then name resolution fails and an ambiguous reference error is reported. Scopes are searched recursively in the following order:

1. The current declaration
2. Declarations the current declaration is nested inside
3. For each containing namespace from inside out
    1. Using directives
    2. Declarations in the namespace
    3. Declarations in nested namespaces that are visible from the current declaration

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

Namespaces are searched from inside out for a name. If a name in a nested package is visible outside that package (i.e. it is public or published), they it is in scope inside that namespace and all namespaces nested inside it. This means that all declarations will be visible in the global namespace. If an identifer corresponds to multiple identifiers when viewed from a given namespace, then references to it are ambiguous and must be disambiguated either by overloading, or by a using statement.

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
    using system.collections; // Without this, references to List[T] would search up to the global namespace and be ambigious.
    using example1.Foo[int]; // This makes `Bar() -> int` visible.
}
```

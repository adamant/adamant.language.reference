# Namespaces and Using Directives

*Namespaces* provide a way of organizing types and functions to avoid conflicts caused by declaring items with the same name. When references to names are ambiguous, they can be disambiguated but qualifying them, or with *using directives*.

## Namespaces

All declarations in an Adamant program are in a namespace. This may be the global namespace which is an unnamed namespace that forms the root of the namespace hierarchy.

The namespace of the declarations in a code file are automatically determined by the file's location in the directory structure. The package determines a root directory for the source. Each directory or subdirectory establishes a new namespace nested inside the namespace of the containing directory. All declarations in code files inside that directory are in that namespace unless they are inside a namespace declaration.

The package can specify a root namespace. If a root namespace is specified, then the package root directory does not represent the global namespace, but the specified root namespace.

### Namespace Declarations

Further namespaces can be created both by directories and by a *namespace declaration*. A namespace declaration scopes the declarations inside it within one or more namespaces. Without the global namespace qualifier, these namespaces are nested inside the file namespace and any containing namespace declarations. A namespace declaration cannot have attributes or access modifiers.

```adamant
// File: foo/example.ad
namespace bar.baz
{
    // declarations in the `foo.bar.baz` namespace
}
```

### Global Namespace Declarations

A namespace declaration can be used to qualify declarations in it with a namespace that is not nested inside the file namespace using the *global namespace qualifier*. Namespace declarations with this qualifier do not nest inside the file namespace nor any containing namespace declarations. Instead, they declare one or more namespaces starting from the global namespace. The global namespace qualifier can also be used to place declarations directly in the global namespace.

```adamant
// File: foo/example.ad
namespace ::.bar.baz
{
    // declarations in the `bar.baz` namespace (NOT in `foo`)
}

namespace ::
{
    // declarations direction in the global namespace
}
```

## Namespace Visibility

Namespaces do not have a declared visibility (no access modifier). Within a package all namespaces are visible to all code. It is the declarations within those namespaces that are restricted. For example, a using directive can be declared to use a namespace that contains no visible members. Only namespaces with "`published`" declarations or published namespaces inside of them are published from a package. Thus a using directive referring to a namespace with no members published from a package would be invalid.

## Name Resolution

Namespaces are searched from inside out for a name. If a matching declaration is found in the namespace, then resolution stops. Otherwise, declarations in nested namespaces visible from the current code are searched. All declarations in nested namespaces are considered together so that declarations in two different sub-namespaces could cause ambiguity. Name resolution then continues to the containing namespace. This means that all declarations will be visible in the global namespace. If a declaration with the same name whose type doesn't match the usage is found, name resolution continues.

## Using Directives

Using directives can appear at the top of a source code file before any other declarations, or inside a namespace declaration before any other declarations. As namespaces are searched inside out, using directives are searched before the containing namespace. Using directives pull in names for the given namespace from all packages. If a using directive references a namespace, it brings into scope only declarations directly in the namespace, not in namespaces nested inside it.

```adamant
namespace example1
{
    public class List[T] {} // A declaration that conflicts with the list in the standard library
}

namespace example2
{
    using system.collections; // Without this, references to List[T] would search up to the global namespace and be ambiguous.

}
```

## Summary of Name Resolution

Name resolution searches until it finds a matching declaration. If a given scope has multiple matching declarations, then name resolution fails and an ambiguous reference error is reported. Scopes are searched recursively in the following order:

1. The current declaration
2. Declarations the current declaration is nested inside
3. For each containing namespace from inside out
    1. Using directives
    2. Declarations in the namespace
    3. Declarations in nested namespaces that are visible from the current declaration

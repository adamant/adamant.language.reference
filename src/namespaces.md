# Namespaces and Using Directives

*Namespaces* provide a way of organizing types and functions to avoid conflicts caused by declaring items with the same name. When references to names are ambiguous, they can be disambiguated by qualifying them, or with *using directives*.

## Compilation Units

**TODO:** write up compilation units

## Namespaces

All declarations in an Adamant program are in a namespace. This may be the global namespace which is an unnamed namespace that forms the root of the namespace hierarchy or a named namespace.

The namespaces containing the declarations in a code file are automatically determined by the file's location in the directory structure. The package determines a root directory for the source. Each directory or subdirectory establishes a new namespace nested inside the namespace of the containing directory. All declarations in code files inside that directory are in that namespace unless they are inside a namespace declaration.

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

Using directives can appear at the top of a source code file before any other declarations, or inside a namespace declaration before any other declarations. As namespaces are searched inside out, using directives are searched before the containing namespace. Using directives pull in names for the given namespace from all packages. Note that using directives must always use fully qualified names starting from the global namespace. If a using directive references a namespace, it brings into scope only declarations directly in the namespace, not in namespaces nested inside it. The order of using directives doesn't matter. All the names brought into scope by using directives are searched simultaneously. Thus if two using directives bring in the same name uses of that name will be ambiguous.

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

Name resolution is package aware. Using directives bring into scope names from that namespace across all packages. However, namespaces searched because they contain the current declaration only bring into scope names in that namespace from the current package. This allows a package to intentionally, declare types and functions with the same name and namespace as referenced package and have them take precedence over those in the referenced package. It also ensures that updates to referenced packages won't break builds by introducing names matching those already in the current package. Only as a final step are names from referenced packages considered.

Name resolution proceeds based on lexical scope from the scope a usage is in outward. When searching outward through namespaces, a compound namespace declaration (e.g. `namespace foo.bar`) is treated as if each namespace name were declared separately nested inside those before it.

Name resolution searches until it finds a matching declaration. If a given scope has multiple matching declarations, then name resolution fails and an ambiguous reference error is reported. Which declarations are considered matching is based on what kind of entity it is and how the named is used. For example, a function call will not match type names. Namespaces are treated somewhat specially with regards to matching. A qualified name is resolved by resolving the first segment of the name and then looking up each subsequent segment in the previous namespace. If any of these later lookups fails to find a match, then the first segment is considered to not match and resolution continues for it. If during this process one of the segments matches to a type or namespace name, this is considered a match.

When the global namespace is reached, first names in the global namespaces of the current package are considered. Then all names nested inside the global namespace are considered. Then names in the global namespace across all packages are considered. Then names nested in the global namespace across all packages are considered. If a namespace declaration with the global qualifier is reached, name resolution stops there as if it was the root of the lexical scopes and there were no lexical scopes containing it.

In summary, the name resolution searches lexical scopes according to the following sequence:

1. The current declaration (i.e. the current function, class etc.)
2. Declarations the current declaration is nested inside (i.e. a class containing a nested class), going from innermost to outermost.
3. For each containing namespace from inside out including the global namespace, search:
    1. Using directives: bring in all names in a namespace regardless of package, do not bring in names in nested namespaces
    2. Declarations in the namespace in the current package
    3. Declarations in nested namespaces in the current package that are visible from the current declaration
4. Declarations in the global namespace across all packages
5. Declarations nested in the global namespace across all packages

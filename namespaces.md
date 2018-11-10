# Namespaces

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

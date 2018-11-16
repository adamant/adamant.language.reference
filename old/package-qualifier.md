# Package Qualifier Operator

The package qualifier `::` is used to qualify which package to resolve a name in. Sometimes a name may be declared in multiple packages. In that case, prefix the name with the package name followed by `::`. Names following this operator are resolved from the root of that package.

`::` can be used to resolve a name in the global namespace by not prefixing with a package name.

## Package Alias

In the project file you can specify a package alias. This name replaces the standard package name for the purposes of the package qualifier.
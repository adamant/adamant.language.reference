## Global and Package Qualifiers

## Package Qualifier

The package qualifier `::.` can be used to qualify which package to resolve a name in. Sometimes a name may be declared in multiple packages. In that case, the name can be disambiguated by prefixing it with the package name followed by `::.`. Names following this operator are resolved from the root of that package.

### Package Alias

In the project file you can specify a package alias. This name replaces the standard package name for the purposes of the package qualifier.

**TODO:** Maybe there should be a special way of referring to the current package. Rust uses `crate::` as the qualifier for the current crate. The word "`package`" has been reserved so it could be used for this.

## Global Qualifier

To start name resolution from the global namespace, a name can be prefixed with the global qualifier "`::.`". This is the same operator used for qualifying names with a package, but no package name is specified.

## Package Qualified Using Directives

A using directive can qualify the namespace with a package qualifier. This restricts the using statement to bring in names inside that package only. Package qualifiers are either the official package name or if an alias for the package is specified, the alias for the package. Note the dot after the double colon. Use of the global qualifier in using directives is not supported because names are always fully qualified starting from the global namespace.

```adamant
using my.package::.foo.bar;
```

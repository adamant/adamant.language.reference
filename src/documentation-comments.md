# Documentation Comments

Adamant programs support documentation using documentation comments.

**TODO:** Need a way to have package level comments (Not just namespace).

## Documentation Blocks

A documentation block consists of one or more single line comments beginning with "`///`". The comment text is interpreted using a subset of Markdown. Documentation blocks must proceed a declaration they are documenting.

```grammar
documentation_comment_block
    : documentation_comment_line+
    ;

documentation_comment_line
    : "///"
```

Example:

```adamant
/// And this is a doc comment for the next declaration
/// It can contain some markdown formatting.
public func() -> void
{
}
```

## Namespace Documentation

Documentation for a namespace can be included by placing a file named "`README.md`" inside the directory for the namespace. The entire contents of the file are taken as the documentation for the namespace.

## Supported Markdown

Documentation comments support a subset of [CommonMark](https://commonmark.org/), a strongly defined, highly compatible specification of Markdown. The following features of CommonMark are not supported:

* Inline HTML
* Trailing pound signs on ATX headings
* Setext headings
* Indented code blocks
* List items with "`+`"
* Whitespace at the end of a line does not cause a hard line break

Additionally, numbered lists will be supported, but there interpretation may be modified.

## Conventions

Use top level Markdown headers in documentation comments (i.e. "`# Header`"). These will be converted to the apropriate level of headers when documentation is generated.

Document conditions outside of the preconditions that will cause abandonment with:

```adamant
/// # Abandonment
```

If a function or type is unsafe, explain which invariants the caller is responsible for upholding.

```adamant
/// # Safety
```

Examples can be included in an examples section.

```adamant
/// # Examples
```

**TODO:** provide a way to document conditions that will cause exceptions to be thrown

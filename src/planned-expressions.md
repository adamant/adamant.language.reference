## Additional Expressions

### Multiline String Literals

Multiline string literals start with three double quotes followed by only whitespace on the line. They continue until a line that begins with whitespace followed by three double quotes. The lines with the double quotes are not included. So the final line does not end with a line break. They can be indented. The indent of the first line is ignored, indentation beyond that is included in the string. Double quotes can be used in a multiline string. A line can be continued by ending it with a backslash. *Regardless of the line endings of the source file, lines in the string are terminated with '\n'.* Multiline string literals should also support string interpolation. (see [Swift Multiline String Literals](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html) for more information).

```adamant
let s = """
    Indent to the left of this is ignored
        This is indented one tab
    This line continues \
    onto the next line.
    """;
```

### Unchecked Expressions

Inside an unchecked expression, the basic operators on integer types would be unchecked. Thus overflow on "`+`", "`-`", "`*`" and "`/`" would not be checked. Note that wrapping on division can occur for `MIN / -1` on signed types. Because the result is `-MIN` which is too large of a positive value. Also note that unary "`-`" can likewise overflow.

Like unsafe, both expression and block forms would be allowed:

```adamant
let x = unchecked(y + z);

unchecked
{
    let a = b * c;
}
```

An unchecked expression is treated the same as an unsafe block. That is, you must now mark the function as "`safe`" or "`unsafe`". This supports the idea that unchecked math is used when you believe the wrapping won't occur but don't want checking to be done. When wrapping is expected, there are the "`wrapping_`*x*" functions. This is also why an unchecked expression makes more sense than individual unchecked operators. The wrapping methods should be used when wrapping is intended. Unchecked is used for performance. In that case, you don't want to accidentally miss an operator, you want a whole chunk of code to be unchecked. This is then easier to read.

Additionally, it might be possible to allow operator and function overloading on unchecked by using "`unchecked`" as a modifier on the function. This would allow non-primitive types to participate in unchecked contexts. For clarity, a "`checked`" modifier could be used as well. Both keywords are reserved.

One open question is what the spec should say about wrapping behavior in unchecked contexts. It could specify two's complement wrapping. However, there might be a performance benefit to leaving it more open. We wouldn't want the C style undefined behavior, but allowing different bit representations and segfaults might help for some hardware.

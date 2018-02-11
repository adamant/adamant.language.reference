# Strings

## Lifetime Issues
There is a potential problem with strings and ownership. If a string is an array of bytes, then some string objects will "own" their array slice and need to delete it. But other strings won't really own their array slices, they will be parts of other strings. Those string will need to not delete their array slice. There is some question as to how this should work and how to express it. The Rust prototype demonstrates it should be possible to unify these cases since they are both essentially pointers. The question is how. It seems like there needs to be a lifetime parameter that can be either some lifetime or "own" that indicates how the string holds its array. Of course, then the question becomes how array slices really work. Do they have the same problem with their underlying memory?

## Encoding

Following the principle of [UTF-8 Everywhere](http://utf8everywhere.org/), string are encoded in UTF-8 by default in memory and all other places. The encodings classes must be used to read and write in other encodings.

## Line Endings

To optimally support cross platform development, newlines are represented in code as simply `\n`. Libraries should convert them when needed for output (for example to the console). Text reading libraries should support the new lines types. The default is `Mixed` which can accept a mix of any of the standard newline forms and converts them to `\n`. When writing text, libraries should support the new line types. The default is line feed `\n`, but other types can be selected, including native. 
# Out of Memory and Stack Overflow

Out of memory errors are handled separately because they are so pervasive.  Any creation of a reference type could lead to an out of memory situation and any function call could lead to a stack overflow situation.

You can avoid causing an out of memory condition by using the `new?` construction which will return none if there was not enough memory to allocate the object. (TODO how to handle failure inside of a constructor though?).

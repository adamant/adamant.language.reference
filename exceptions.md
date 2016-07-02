#Exceptions

# Try/Catch Statements

# Try expressions

Swift has prefix expressions with `try` to allow them to throw.  `try?` turns their result into a nullable rather than throwing.  `try!` makes them panic if an exception is thrown.  Midori had `try <exp> else catch` that returned Result<T>.  You could also do `try <exp> else <value>` that would use value if an exception was thrown.  My concern about those is that they don't specify the exception.  So if a new exception is added, they could swallow it too.

Swift doesn't have finally.  Instead it has `defer`.  I think that is because of definite assignment and no null values.  In a finally, you can't use any variables from the block because you don't know if they have been initialized. Of course, clean up code may be less of an issue since you should do it in destructors.

Adamant

`try` will throw an exception if the expression throws or if the expression returns an error Result<T>.  `try?` returns a `Result<T>` of error if the expression throws.  `try!` will panic if the expression throws or returns an error `Result<T>`.
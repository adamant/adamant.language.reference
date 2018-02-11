# Exceptions

Exceptions are effects and may need to be explained after them. All exceptions thrown must be subtypes of `Exception`.

## May Throw and No Throw

Functions are annotated with a list of exceptions they could throw.

    public Func() -> void
        may throw IOException, ParseException
    {
        // ...
    }

There are no unchecked exceptions. Exception annotations are part of the type of the function. Functions that don't throw any exceptions are marked with `no throw`. Any function that doesn't have a throw clause will have its throws inferred. Only at the public API boundary are you required to explicitly declare all thrown exceptions.

## Throw Expressions

To throw an exception use a throw expression.

    throw new FileException("Foo");

The type of a throw expression is `!` so it is a subtype of any type. This allows throw expressions to be used in interesting ways.

    let x = ReadLine() ?? throw new UserInputException();

## Try Operator

Note: this is an operator to avoid the implication that it should be followed by an expression block like a conditional expression.

Any expression that could throw an exception must be contained in a try expression. In it's simplest form, a try expression simply marks points in the code that could throw an exception.

    let x = try foo();

In this example, any exception thrown by the `foo()` function would be thrown from the expression.

Note: having a try expression when it isn't needed or missing one when it is, is a non-fatal compiler error. That is, it is an error, but a debug build can still produce an executable.

### Catch Expressions

A try expression can have a catch expression as well. This allows exceptions to be handled and turned into values.

    let x = try foo() catch e: Exception => "value";

A try expression with catch expressions will evaluate to the value of the tried expression if no exception is thrown, otherwise it will match a catch expression and evaluate to that value.

There can be multiple catch expressions and they can use expression blocks with braces. To rethrow an exception, it is fine to use throw on the caught exception (there is no `throw;` statement like in other languages). Alternatively, you can wrap the exception in a new type by passing the caught exception to its constructor.

    let x = try foo()
        catch e: FileException => none
        catch e: NetworkException
        {
            if e.IsFatal
                { throw e; }

            => "error value";
        }
        catch e: ParseException => throw new DataException(e);

Exceptions can be caught and transformed into `Result<T>` values.

    // Not sure on syntax for this
    let x = try? foo();
    let y = try foo() catch; // could cause parse issues
    let z = try foo() catch?; // makes catch seem optional
    let x = catch foo();

Alternatively, you can panic when an exception occurs.

    // Note sure on syntax for this
    let x = try! foo();
    let y = try foo() catch!;
    let a = catch! foo();
    // Better to be explicit about the exception type
    let z = try foo() catch! NetworkException;
    // This makes it easy to search code for this
    let a = try foo() catch NetworkException => panic!();

Note: Swift has prefix expressions with `try` to allow them to throw. `try?` turns their result into a nullable rather than throwing. `try!` makes them panic if an exception is thrown. Midori had `try <exp> else catch` that returned Result<T>. You could also do `try <exp> else <value>` that would use value if an exception was thrown. My concern about those is that they don't specify the exception. So if a new exception is added, they could swallow it too.

### Catch Patterns

You can only catch declared exceptions, not specific subtypes of declare exceptions because then you are depending on knowledge about the implementation of the method and are hiding a runtime type check. Not allowing this means exceptions don't have to carry runtime type info.

Note: This idea comes from Midori.

**TODO:** This implies sum types almost. If a function `may throw NetworkException, FileException` effectively, it throws the type `NetworkException|FileException`. If the result type is `Result<T, E> where E: Exception` then we might want to be able to say `Result<T, NetworkException|FileException>`. Likewise the function could be declared `may throw NetworkException|FileException` and type aliases could be `alias IOException = NetworkException|FileException;`.

**Should catch clauses have to be exhaustive?** Probably makes sense to start in the more restrictive form.

    let x = try foo
        catch NetworkException => "value"
        throw FileException; // rethrow these

    let y = try foo
        catch NetworkException => "value"
        catch e => "value"; // e: Exception

    let x = try foo catch _ => "value";

## Throwing an Error `Result<T>`

If a function returns `Result<T>`, the error case can be converted to an exception.

    // Not sure of syntax here
    let x: T = try bar(); // bar doesn't throw, it returns Result<T>
    let y = throw? bar(); // Throw on error

## Try/Catch Statements

    try
    {
        let x = try foo();
        return x + 1;
    }
    catch e: IOException
    {
        // handle exception
    }

As with catch expressions, the catch statements can only be for exception types declared to be thrown.

Note: Swift uses the `do` keyword for blocks instead of the `try` keyword. However, the `try` is consistent with the use of it as a marker of code that may have exceptions passing through it. It also leaves the `do` keyword available for other uses. Given that try expressions will clearly mark where exceptions can be thrown. We don't necessarily need to precede try blocks with a keyword. We could simply allow a catch on any block containing a try expression. However, this could make control flow confusing because when looking at the try expression it might not be clear whether there could be a try block below.

## Defer

Note: Swift doesn't have finally. Instead it has `defer`. I think that is because of definite assignment and no null values. In a finally, you can't use any variables from the block because you don't know if they have been initialized.

**TODO:** Not sure yet wether Adamant needs Swift style defer given the RAII pattern support. Clean up code may be less of an issue since you should do it in destructors. Also, there may be lifetime issues.

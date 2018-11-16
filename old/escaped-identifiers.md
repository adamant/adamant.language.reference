# Escaped Identifiers

The backslash is used to escape keywords and reserved words for use as identifiers. For example ``\class``, allows one to use the `class` keyword as a variable name. Only keywords, contextual keywords and reserved words are allowed to be used as escaped identifers. For example, `\hello` is a syntax error because "hello" is not a keyword.

    let \class = "Mammalia";
    let order = "Carnivora";
    let family = "Canidae";
    let genius = "Canis";
    let species = "lupus";

    console.WriteLine("The Gray would is {\class} {order} {family} {genius} {species}");


## Identifier Strings

For more flexibility in variable names, strings can be used as identifiers names by escaping them with a backslash. This is particularly useful for tests.

    @test
    public \"`foo()` returns `true`"() -> void
    {
        assert(foo());
    }

Identifier strings are like literal strings (except for newlines). Escape sequences are not evaluated and values are not interpolated. To include double quotes in an identifer string use two double quotes. Newlines are not permitted in identifier strings.

    let \"He said ""Hello"" to me." = true;

It is an error to use a identifier string when it isn't necessary.

    let x = 1;
    let y = \"x"; // error: `\"x"` is a legal identifer, use `x` instead
    let \"class" = 5; // error: `\"class"` is a keyword identifier, use `\class` instead

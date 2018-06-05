# Rejected Feature Ideas

## `if not` and `while not`

*Rejected Reason:* adoption of control flow requiring blocks but not parenthesis (Rust style) means that this feature doesn't really makes sense as it is already essentially supported because control flow statements don't have parenthesis.

Languages like Ruby have inverted control flow with `until(condition)` for `while(not condition)` and `unless(condition)` for `if(not condition)`. In languages without that there is often a need for an extra set of parens which can be ugly and confusing (i.e. `if(not(a and b)) statement;`. What if the language allowed a not outside the required parens?  So that could become `if not(a and b) statement;`. Of course, if we switch to control flow not requiring parens this becomes a non-issue (i.e. `if not(a and b) { statement; }`). Though there is some extra clarity when parens are required, because you know the not must apply to the whole condition, not just the first part of it.

## Alternatives for Escaped Identifiers

*Rejected Reason:* The backslash syntax was adopted.

Currently, backtick `` ` `` is used to escape identifiers and double backtick creates escaped string identifiers.

| Font         | Escaped Keyword                               | Escaped String |
| ------------ | --------------------------------------------- | -------------- |
| Monospace    | `` `class``                                   | ``` ``A name with spaces`` ``` |
| Proportional | \`class                                       | \`\`A name with spaces\`\`          |

However, this conflicts with the use of markdown in comments when referring to code. While escaped identifiers are rare, it can still be confusing. Unfortunately, preventing this would mean not being able to use the backtick as a symbol in the language. Still, this might be worth it. Note in all these examples, that the single symbol form may be restricted to keywords and reserved words only. So it may not be legal to use `` `hello``. This can make the syntax less ambiguous. Possible syntaxes:

* `^class` and `^^This is a test^^` (exponent would be `**`)
* `^class^` and `^This is a test^`
* At sign `@class`, but what about names with spaces? Confusing around attributes
* Square brackets `[class]` and `[This is a test]` (this conflicts with tuple syntax, for example `let [x,y] = x` does that destructureor assign to the identifier `[x,y]`?)
* `[[class]]` and `[[This is a test]]`
* `*class` or `class*`, and `**This is a test**` (dereference could be caret `^` and exponent double caret `^^`) (Suffix asterisk makesit seem like the star of a footnote which I like) (Double star reads like bold or shouting though)
* `--This is a Test--`
* `~~This is a Test~~`
* `'class` and `''This is a test''` vs string `"This is a test"` but in variable width font that is ''This is a test'' vs string "Thisis a test"
* `'class'` and `'This is a test but don't use apostrophes'`
* `#class` and `##This is a test##`

For some of these syntaxes, the symbol could be taken to mean "literal" and used as the prefix for literal strings. However, it was suggested by Alex Burkhart that string escaped string identifiers could instead be created by prefixing strings with the same character. These strings would work like literal strings in terms of what characters they accept. Possible syntaxes for that are:

* `#class` and `#"An identifier"`
* `'class` and `'This is a test'` and `'"A literal string\"`
* `\class` `\"This is a test"`  (the blackslash is used for escape sequences, i.e. it escapes from the current mode. In this caseescaping from regular parsing of keywords and strings)  The blackslash could possibly be combined with other characters to create otherescape like things, perhaps around macros.
* `@class` `@"This is a test"` (but this is confusing with attributes)
* `*class` `*"This is a test"`

Finally, perhaps using backtick isn't so bad. It is unlikely that a code snippet in markdown begins with a backtick. If we could eliminate the double backtick by putting backtick in front of strings then code could always be surrounded by double backtick in markdown.

| Font         | Escaped Keyword                           | Escaped String |
| ------------ | ----------------------------------------- | -------------- |
| Monospace    | `` `class``                               | `` `"A name with spaces"`` |
| Proportional | \`class                                   | \`"A name with spaces"          |

However, this seems like a very easy to overlook syntax.

## Other Uses of Backtick

*Note:* There was some thought of using backtick in other places when it was the escape character. Those are recorded here.

Backtick may also be a convenient way to mark other literals. For example:

* Date times - `` `2015-03-03``
* Intervals - `` `[3..6)`` or `` `[3,6)``

Perhaps backtick could be used for operator overloading?  So `` `||`` would be the method name for overloading operator '`||`'  However, that might be confusing.

## Don't Distinguish between Non-interpolated and Verbatim String Literals

*Note:* When string interpolation was done using curly braces, and there were verbatim strings, there was some thoughts that verbatim strings may not be that important of a feature.

It may not make sense to have non-interpolated string literals as distinct from verbatim ones. The only thing that lets one do is write curly braces without escaping them. That also opens up the idea of using a syntax that doesn't support distinguishing the two, like the number sign.

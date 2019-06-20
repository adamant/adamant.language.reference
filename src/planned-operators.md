## Operator Features

### Operator Overloading for `?.` and `??` Operators

These operators should support overloading. It isn't clear exactly how this should work. Presumably, the behavior of the two operators should somehow be linked. It is an outstanding question whether there are every situations where the two operators need to be overloaded fully independently. As a general rule, having them independent would provide greater flexibility to developers who might find one or the other operators useful is a very different context.

The first step of both is to evaluate their left hand side operand. The result of which is either a valid value to continue executing with, or a "none" value. In the case of the `?.` operator the "none" value should be returned without evaluating the rest of the expression. In the case of the `??` operator the "none" value causes the right hand operand to be evaluated and its value used. It was thought this could be done by an overload of the "`?`" pseudo operator which returned an optional type. However, this does not allow the none value to be anything besides `none`.

The coalesce operator would require its own overload. This would either take as a parameter the left hand value or perhaps the "none" value returned by the first step of evaluation. This could be very useful. For example imagine coalescing two `Result[T, E]` values where both are errors. The first step would unwrap the error value from the left hand side. The second step would then combine this with the error value from the right hand side to produce a composite error. If this operator overload is fully independent from the `?.` then it may make sense to have apply the same approach as is used when overload `and`, and `or`.

## Logical Operator Overloading

Support for overloading the logical operators `and`, `or`, and `not`. Note that the `not` operator is included here for consistency rather than because of any issues supporting overloads of it.

The logical operators `and` and `or` are special because they perform short circuit evaluation. That is, their second argument may not be evaluated. Thus they can not be simple method calls. One approach would be to make the second argument a closure of the rest of the expression. However, the performance of that would need to be evaluated before that could be adopted. Another approach, similar to the one used by C# is to evaluate these operators in two steps. This approach is described below.

Each operator has two overloads. The first determines when the second argument isn't evaluated, the second provides the value when it is. The first must return an optional type. If the result is `none` then the second argument will be evaluated and the binary form called. The two overloads for an operator must be overloaded together. When overloaded for some type, `x and y` is effectively evaluated as `x.and() ?? x.and(y)` and `x or y` is effectively evaluated as `x.or() ?? x.or(y)`. Note the first argument will only be evaluated once. Not that for the `and` operator, the unary version returns either its "false" value or `none` when it is "true". Another challenge of this approach is that the unary version of the operator can't easily be overloaded based on the type of the right operand. In the example code below, this is handled using a type parameter. However, this is awkward without a type equality operator for the where clause. Even with one, it makes overloading have to account for constraint clauses.

An issue with this approach is that one may want to transform the value before passing it to the second overload. Perhaps the result needs to be some other type that allows a value to be passed in the "none" case? Another issue is that the placeholders for the operands will fuse with the token for the operator because `_and_` and `_or_` are valid identifiers. This may require spaces or special handling in the compiler.

```adamant
// In `fuzzy_bool`
public operator[Other] _and_(self) -> fuzzy_bool?
    where Other: fuzzy_bool
{
    return if Value == 0 => self else => none;
}

public operator and(self, other: fuzzy_bool) -> fuzzy_bool
{
    return new fuzzy_bool(value.min(other.value));
}

public operator[Other] _or_(self) -> fuzzy_bool?
    where Other: fuzzy_bool
{
    return if value == 1 => self else => none;
}

public operator _or_(self, other: fuzzy_bool) -> fuzzy_bool
{
    return new fuzzy_bool(value.max(other.value));
}
```

## Member Access Operator Overloading

It may be necessary or useful to allow overloading of the member access operators "`.`". However, it is unclear how to handle access to methods on a type that overloads the member access operator. Note that the ability to overload the dereference operator "`^`" partially mitigates the need for overloading the member access operator because it allows for types that act pointer like. However, there may be situations where types need to behave like references and support member access.

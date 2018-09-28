# Composite Initializers

Adamant supports three different forms of composite initializers. Similar user defined literals, these initializers do not determine the type being constructed. The type constructed is inferred. These composite initializers are named after the type they are generally used to construct, but they actually could be used to construct any type. All of them are prefixed with a pound sign that is followed by a comma separated list of values, the difference it simply in what those values are bracketed with. The three types of initializers are:

| Initializer | Syntax    |
| ----------- | --------- |
| Tuple       | `#(x, y)` |
| List        | `#[x, y]` |
| Set         | `#{x, y}` |

## Construction

Composite initializers invoke special constructors whose names mirror the syntax. There arguments can be one of a number of forms.

```adamant
public class Example
{
    // Tuple Constructor with Regular parameters
    public implicit new #() (x: int, y: int)
    {
        // construct
    }

    // List Constructor with params constructor
    public implicit new #[] (params values: Array[int])
    {
        // construct
    }

    // Set Constructor with params constructor
    public implicit new #{} (params values:)
}
```

## Typed Construction Syntax

If needed or desired for clarity, the type of a composite initializer can be specified as `new Example #{x, y}` (or one of the other two initializer types). If the constructor is declared implicit, it can be called without specifying the type. If it is not implicit, the type must be stated.

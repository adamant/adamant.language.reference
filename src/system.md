## `system` Namespace

### `system.Iterable`

### `system.Iterator`

#### Enumerate

The standard library provides an `enumerate()` method for counting. This provides the index of each value in the iterator sequence.

```adamant
foreach #(i, x) in (5..10).enumerate()
{
    console.write_line("i = \(i), x = \(x)");
}
```

Outputs:

```console
i = 0, x = 5
i = 1, x = 6
i = 2, x = 7
i = 3, x = 8
i = 4, x = 9
i = 5, x = 10
```

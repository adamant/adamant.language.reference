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

### Ranges

*Note:* The exact range API has not been determined. There may need to be a distinction between ranges of continous types and ranges of descrete types. See Swift 3 ranges and Stridable for an example https://oleb.net/blog/2016/09/swift-3-ranges/.

Any type `T where T: Integral` implements the range operator `X..Y` so that is returns a `Range[T, 1]` inclusive of `X` and `Y`. If `X > Y` then the range returned is the empty range. Note the type of `Range` is `Range[T, Step:T=1] where T: number`. This needs to fit in with an `Interval` type defined in `system.math`. To construct a range exclusive of the end value use the `..<` operator. Likewise the `<..` and `<..<` operators are used to construct ranges exclusive of the start and exclusive of both the start and end respectively.

Related types:

* ReverseRange - instead of building negative step into range
* RangeFrom - range only bounded below
* RangeTo - range only bounded above
* RangeFull/FullRange - unbounded range
* InclusiveRange - range inclusive of the end value
* StepBy? - a way to represent stepping ranges
* Interval
* Distribution used by random etc.

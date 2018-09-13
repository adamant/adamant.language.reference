# Ranges

Any type `T where T: Integral` implements the range operator `X..Y` so that is returns a `Range<T, 1>` inclusive of `X` and `Y`. If `X > Y` then the range returned is the empty range. Note the type of `Range` is `Range<T, Step:T=1> where T: number`. This needs to fit in with an `Interval` type defined in `System.Numerics`. To construct a range exclusive of the end value use the `..<` operator. Likewise the `<..` and `<..<` operators are used to construct ranges exclusive of the start and exclusive of both the start and end respectively.

Related types:

* ReverseRange - instead of building negative step into range
* RangeFrom - range only bounded below
* RangeTo - range only bounded above
* RangeFull/FullRange - unbounded range
* InclusiveRange - range inclusive of the end value
* StepBy? - a way to represent stepping ranges
* Interval
* Distribution used by random etc.

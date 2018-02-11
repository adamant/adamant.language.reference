# Ranges

Any type `T where T: integral` implements the range operator `X..Y` so that is returns a `Range<T, 1>` inclusive of `X` and exclusive of `Y`. It must be the case that  X < Y. Note the type of `Range` is `Range<T, Step:T=1> where T: number`  This needs to fit in with an `Interval` type defined in System.Numerics.

Related types:

   * ReverseRange - instead of building negative step into range
   * RangeFrom - range only bounded below
   * RangeTo - range only bounded above
   * RangeFull/FullRange - unbounded range
   * InclusiveRange - range inclusive of the end value
   * StepBy? - a way to represent stepping ranges
   * Interval
   * Distribution used by random etc.

Currently `..` is exclusive of the end and `to` is used for inclusive of end.

---

## Notes

Even though `..` is already used as the accept operator, those uses should be distinct enough that it can also be used as a range operator as in Rust. However, if `...` is to mean tuple/list expansion for variadic type parameters, it can't be used to mean range inclusive of the end value. Also, it might be easily confused. It would be nice if the range syntax permitted a natural extension to intervals on the real numbers. Unfortunately, without some prefix the parens/brackets notation of standard intervals is not easily parsable. If a prefix was used then it could be made to work. For example, `#(1..6]` would be exclusive of 1 and inclusive of 6. Alternatively, they could be move inside like `1(..]6` but I just find that strange. Or some other notation could be adopted. Rust has suggested `..=` for inclusive of the end value, but I don't like that. 

Or scala style `to` for ranges inclusive of end and `until` for ranges exclusive of end. i.e. `for x in 1 to 5` and `for x in 0 until length`

Or what about `1.,5` for inclusive? Or what about `1.to(5)` but that might be a problem for patterns

Or `0..<length` and `1<=..<=5` for inclusive and `1<..<5` exclusive of both.

Really like `..` with `||` for size. So `for let x in 0..|array|` `for let x in 1 to 5`

    [1--5) // Looks good but could mean

    1[.]6
    4(.]6
    1``6

    4**6

    4^5 // exponent

    1~6 // exclusive of last
    1~~6 // inclusive of last
 
 But it would be good if range syntax was useful for intervals too.
 
 Since `..` is supposed to be the accept operator, what if we made ranges a little more like in math with intervals. Of course something would need to distinguish them from other things. Perhaps `#[0,5)` would be the range from 0 to 5 (not inclusive of 5). Note that `` `[0,5)`` has be proposed as an interval syntax as well.
## Bitwise Operations

Bitwise operations are provided on all the integer numeric types including "`size`" and "`offset`". However, they are not operators. Instead they are methods. However, they will always be inlined and their use does not involve the overhead of a method call.

| Operation                  | Meaning                                 |
| -------------------------- | --------------------------------------- |
| `x.bit_complement()`       | The bitwise complement of `x`           |
| `x.bit_and(y)`             | The bitwise and of `x` and `y`          |
| `x.bit_or(y)`              | The bitwise or of `x` and `y`           |
| `x.bit_xor(y)`             | The bitwise xor of `x` and `y`          |
| `x.bit_shift_left_by(y)`   | The value of `x` shifted left `y` bits  |
| `x.bit_shift_right_by(y)`  | The value of `x` shifted right `y` bits |
| `x.bit_rotate_left_by(y)`  | The value of `x` rotated left `y` bits  |
| `x.bit_rotate_right_by(y)` | The value of `x` rotated right `y` bits |
| `x.count_one_bits()`       | The number of one bits in `x`           |
| `x.count_zero_bits()`      | The number of zero bits in `x`          |

For the shift and rotate operations, the `y` value is unsigned. When shifting signed types right, the value is sign extended. When shifting unsigned types right, the value is zero filled.

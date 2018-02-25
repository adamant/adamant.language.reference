# Reserved

## Words

| Word          | Possible Uses                                  |
| ------------- | ---------------------------------------------- |
| `case`        |
| `default`     |
| `defer`       |
| `do`          | `do while` loop or `do` block                  |
| `fallthrough` |
| `from`        |
| `guard`       |
| `repeat`      | `repeat while` loop (Swift)                    |
| `then`        | loop else* (Python)                            |
| `null`        | null pointers                                  |
| `switch`      |
| `select`      |
| `unless`      | `if not` or `unless break` loop else (Python). |
| `when`        | exception filters (C#)                         |
| `where`       |

\* This even allows for `while condition { } then { } else { }`. That is loop, do else if the loop never executes, do then block if the loop executes and ends without breaking. Note though that a `then` without an `else` would execute even if the loop never did. (see also `unless break`)

## Operators

* `**` exponent
* `` ` `` backtick reserved for use as markdown code

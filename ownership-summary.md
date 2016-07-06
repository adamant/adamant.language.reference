# Summary of Ownership

## Passing

Kind                       | Move   | Copy*  | Borrow
-------------------------- | -----: | -----: | -----:
Move Value (`struct` w/o `implicit copy`)      |      x | copy x |  ref x
Copy Value (`struct` w/ `copy`) | move x |      x |  ref x
Reference (`class`)        | move x | copy x |      x

* for Move Value and Reference types, a copy method must be implemented to do this

## Types

Kind                       | Move  | Copy  | Borrow
-------------------------- | ----: | ----: | -----:
Move Value (`struct` w/o `implicit copy`)      |     T |     T |  ref T
Copy Value (`struct` w/ `implicit copy`) |     T |     T |  ref T
Reference (`class`)        | own T | own T |      T
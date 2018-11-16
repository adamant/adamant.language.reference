# Bitwise Operations

While bitwise operations could be operators like `bit_and` etc. It would be totally reasonable to make them methods like `x.BitAnd(y)`. I just need to assure that they won't incur method call overhead. Making them methods simplifies the language and demotes them to rarely used features.
# Type Aliases

The keyword `alias` introduces an alias for a type name.

    alias PromiseResult<T> = Promise<Result<T>>

By default aliases are private, however they can be made public and used from other places.

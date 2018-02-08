# Effects

Generics over effects.  For example (Code from Midori, syntax may differ):

	 Map<T, U, effect E>(T[] ts, Func<T, U, E> func) -> U[] E
	{
		U[] us = new U[ts.Length];
		for (int i = 0; i < ts.Length; i++) {
			us[i] = effect(E) func(ts[i]);
		}
		return us;
	}

## Other Effects

Effects are written after the return type.  Any given effect is marked as `may` or `no`.  Omitted effects are inferred.  Throws clauses are effects.  The effects supported are:

  * unsafe - whether unsafe blocks can be executed
  * panic - whether a function can cause a panic, note that since contracts and assertions could cause panics, this requires that the compiler be able to prove that the contracts are satisfied. (What if you could distinguish different kinds of panics?  Like contracts vs arithmetic overflow?)
  * allocate? - whether memory can be allocated (besides the stack?)
  * io? - separate for network, file and console? other graphics?
  * init? - prevent constructors from calling other methods? see [No Leaky Abstractions](http://joeduffyblog.com/2016/11/30/15-years-of-concurrency/)
  * pure?
  * out of memory and stack overflow

---

Notes from 2017-10-15

Just as with inferred throws, it makes sense to have all effect types inferred except at public API boundaries where they have to be explicit.  How do you support generic kinds of effects.  How do you infer that an effect doesn't propagate.  The syntax for effects could be `may` or `no`.  So after the return type of a function could say `may effect` or `no effect`.  Effects would be listed comma separated for example `may panic, throw(exception1, exception2)`.  In theory `throw exception1, throw exception2` are the same.

Effects:
  * throw
  * panic - preconditions and post conditions could cause panic.  So I need to prove that they hold to be able to say something won't panic
  * out of memory - separate because it seems more pervasive
  * overflow could be a panic
  * unsafe - even functions marked safe that call unsafe code would be `may unsafe`
  * is trusted an effect?

Do effects have a hierarchy?  How fine grained are effects.  For example, a method that panics could do so because of overflow or precondition.  Are there effects for IO? Is there a different effect for file and network IO. Given pervasive async/await, it seems like blocking shouldn't be an effect.  However, a long running computation could kind of be like blocking.

Perhaps it is valid to mark a function with a precondition as not panicking because the check of the precondition is effectively in the calling code not in the function itself?  (At call site because some call sites prove the precondition is satisfied).

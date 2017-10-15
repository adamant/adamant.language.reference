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
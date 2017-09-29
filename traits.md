There are no explicit interfaces.  Instead you can implement the interface of a class by placing it after the second inheritance colon.

	public class Example: : SomeClass
	{
	}

This has a number of consequences:

  * private members are private to the instance not shared between instances of the class
  * non-private fields can be overridden with other fields or properties. Consequently, there is no reason to use properties when a field will do.
There are no explicit interfaces. Instead you can implement the interface of a class by placing it after the second inheritance colon.

    public class Example: : SomeClass
    {
    }

This has a number of consequences:

  * private members are private to the instance not shared between instances of the class
  * non-private fields can be overridden with other fields or properties. Consequently, there is no reason to use properties when a field will do.

It would be really good to make interfaces more like traits and allow implementations of some methods in them, but it isn't clear how to mark these. Extending a class would apply the methods to classes implementing the interface, so perhaps that is the proper way to do that.
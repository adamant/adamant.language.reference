# Destructors

Destructors have an implicit `mut self` parameter.  They do not take ownership of self so that they can't resurrect the instance.  All types have an implicit destructor that calls delete on any owned objects.  Destructors are executed most derived class first moving toward the base class.

TODO while it makes sense to allow destructors to call member functions like C++.  Obviously, they shouldn't call `open` functions.  But what if they call a method that calls an open function? 

## Exceptions in Destructors

All destructors implicitly are `throws void`.  This is because destructors are called while exceptions are unwinding the stack.

Alternatives:

  1. Allow destructors to throw, but panic if this happens during stack unwinding
  2. Allow destructors to throw, but give them a way to tell if it is during stack unwinding (i.e. std::uncaught_exception()) and choose what happens.  This seems to be discouraged even in C++
  3. Allow destructors to throw, but enforce something about try catch blocks so that types with destructors that throw can never be destructed by stack unwinding.  This seems very restrictive
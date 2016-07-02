# Enums

From http://tratt.net/laurie/blog/entries/another_non_argument_in_type_systems

"The basic idea is that sum types are a super type that ranges over other types. Whereas in OO languages a sub-class explicitly chooses its super-class(es), sum types (the super class equivalent) choose the types they range over (the subclass equivalent) — the latter have no idea which sum types they are ranged over by."

This might be a hint at how to define sum types.  Perhaps you can literally say a type is the sum of existing types



// Possible ways to declare enum values
public enum Example
{
	Name, // A unit typed value, inherits Example
	Constructor(3.14), // A unit typed value calling the constructor of Example with 3.14 
	Assignment = new() // A singleton like value, 
	{
	},
	AssignmentPlus = new :Something:Foo() // A singleton that doesn't actually have the type Example
	{
	},
	Typed: Type, // Like a field that has that type, i.e. discriminated union
	Tuple: (int, int), // Same as before, except the type is a tuple, can also use structs?
	Struct: // An anonymous type that inherits Example
	{
		
	},
	Class: class // basically an alternate to the syntax just above
	{
	},
}

public enum DayOfWeek : int
{
	Sunday = 0,
	Monday,
	Tuesday,
	Wednesday,
	Thursday,
	Friday,
	Saturday,
}

public enum Maybe<T>
{
	null,
	Value: T,  // not sure right syntax here
}

public enum Planet
{
	// TODO use correct case, I just copied a Java example
	MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    EARTH   (5.976e+24, 6.37814e6),
    MARS    (6.421e+23, 3.3972e6),
    JUPITER (1.9e+27,   7.1492e7),
    SATURN  (5.688e+26, 6.0268e7),
    URANUS  (8.686e+25, 2.5559e7),
    NEPTUNE (1.024e+26, 2.4746e7);
	
	public let Mass: double;
	public let Radius: double;
	
	private new (double mass, double radius)
	{
        Mass = mass;
        Radius = radius;
    }
	
	// universal gravitational constant  (m3 kg-1 s-2)
    public let G: double = 6.67300E-11;

    public SurfaceGravity() => double
	{
        return G * Mass / (Radius * Radius);
    }
    public SurfaceWeight(double otherMass) => double
	{
        return otherMass * SurfaceGravity();
    }
}


public enum Currency: Runnable
{
	  PENNY(1)
	  {
			  @Override
			  public String color()
			  {
					  return "copper";
			  }
	  },
	  NICKLE(5)
	  {
			  @Override
			  public String color()
			  {
					  return "bronze";
			  }
	  }, DIME(10)
	  {
			  @Override
			  public String color()
			  {
					  return "silver";
			  }
	  }, QUARTER(25)
	  {
			  @Override
			  public String color()
			  {
					  return "silver";
			  }
	  };
	  private int value;

	  public abstract String color();
	
	  private Currency(int value) {
			  this.value = value;
	  }
	  ..............
}       


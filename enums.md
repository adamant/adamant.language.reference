# Enums

An enum in Adamant is a type that can have one of a number of possible variants. Each variant can optionally have data associated with it. Both classes and structs can be enums. This is indicated by placing the keyword `enum` before the `class` or `struct` keyword. Enums are sometimes referred to as discriminated unions because they union together different types safely with a discriminator value.

    public enum class Result<T>
    {
        Ok(T),
        Error
    }

## Enum Structs

All enums are distinguished by a discriminator field created by the compiler. Enum structs can specify the exact type of this discriminator and the value of it for variants without data. The type of the discriminator follows the `enum` keyword with a colon and the discriminator type.

    public enum:int struct Day_Of_Week
    {
        Sunday = 0, // The value for Sunday is 0
        Monday = 1, // The value for Monday is 1
        Tuesday,
        Wednesday,
        Thursday,
        Friday,
        Saturday,
    }

When a discriminator value is specified for a variant, each variant after without a discriminator value is inferred to have the discriminator one greater than the previous variant.

## Enum Forms

There are a number of forms that an enum variant can take.

```adamant
public enum struct Example
{
    // TODO what are the naming conventions for enum values?
    InferredDiscriminator, // discriminator value unknown
    ExplicitDiscriminator = 1, // discriminator value is 10
    ImplicitDiscriminator, // follows explicit discriminator 1 so discriminator is 2
    Constructed = new(45), // call constructor, don't need to name the class, can call named constructor
    SubclassA
    {
        // new members
    },
    SubclassB:: Interface // subclass implementing interface, again base class implied
    {
        // new members
    },
    SubclassC: OtherBase : Example // inherits another class, implements the interface of Example, not allowed for structs
    {
        // new members
    },
    PatternType(type1, type2) // Allow names? auto create stuff?  Combine with class members?

    // Members
    public let value: int?;

    public new()
    {
        self.value = none;
    }

    public new(value: int)
    {
        self.value = value;
    }
}
```

## Custom Matching

Any type can be pattern matched. To control matching implement the `is` operator (TODO something better? scala uses unapply). This is modeled on C#.

```adamant
public operator is(self, x: ref int, y: ref int)
{
    x = self.X;
    y = self.Y;
}
```

---------
## Old Notes

From http://tratt.net/laurie/blog/entries/another_non_argument_in_type_systems

"The basic idea is that sum types are a super type that ranges over other types. Whereas in OO languages a sub-class explicitly chooses its super-class(es), sum types (the super class equivalent) choose the types they range over (the subclass equivalent) — the latter have no idea which sum types they are ranged over by."

This might be a hint at how to define sum types. Perhaps you can literally say a type is the sum of existing types:

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


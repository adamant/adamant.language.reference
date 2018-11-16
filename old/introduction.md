# Introduction

Adamant is a new language that is under development. It will be a general-purpose language with high performance, deterministic safe resource management, and guaranteed thread safety. Documentation and resources can be found at [adamant-lang.org](http://adamant-lang.org). Adamant should be a compelling alternative to other high level languages like C# and Java. It's focus on compile time safety aids in creating correct code. Other features of interest include:

* object lifetimes
* write once, compile anywhere
* guaranteed optimizations
* asynchronous everywhere
* type inference
* generics with partial specialization
* class defined interfaces
* optional exception specifications
* minimal runtime

## Purpose

This book serves is an informal reference to the Adamant language. It tries to be comprehensive by covering all features of the language. It can serve as an introduction to the language for programmers who are already familiar with another language like C#, Java or Rust. Features unique to Adamant are explained in more detail while features shared with other languages are only briefly covered with a focus on stating how they work and any differences with other languages.

Those less familiar with programming would do better to read the [Introduction to Programming in Adamant](https://github.com/adamant/adamant.language.introduction/blob/master/book.md) book. If you are already very comfortable with computer programming and just want to see what sets Adamant apart from other languages, continue on to the next section for a brief summary of some of the unique features of Adamant. If you'd like to simply learn Adamant. Feel free to skip ahead to the section on the language, [Variable Bindings](variable-bindings.md).

## Unique Features

This section introduces a few of Adamant's more unique features and links out to the relevant section on each feature.

### Ownership, Borrow Checking and Lifetimes

Adamant takes a very different approach to memory management than other high-level languages today. Garbage collection has become the common approach to memory management. Instead, Adamant uses the approach pioneered by Rust of having the compiler statically check rules for memory management and determine when objects should be deleted. For more information see [Ownership](ownership.md) and its subsections.

### Inferred Throws Clauses

Adamant has checked exceptions similar to Java or C++. However, unlike either of those languages, by default the exceptions thrown by a function are automatically inferred. Only on externally exposed APIs are exception specifications required.

```adamant
// Throws clause is required because this functions is publicly exposed
public Function1() -> void
    throws exception
{
    Function2();
}

// Throws clause is inferred because it is omitted and this is an internal function
internal Function2() -> void
{
    throw new exception();
}
```

For more information see [Exceptions](exceptions.md).

### Interfaces of Classes

In many languages there is a distinction between an interface or trait that has no implementation and a class which can have implementation. Adamant doesn't have separate interfaces. Instead, any class can implement the implicitly defined interface of another class. In a class declaration, the base class appears after the first colon and classes whose interface is being implemented appear after the second colon.

```adamant
public class MyClass: BaseClass <: ClassAsInterface1, ClassAsInterface2
{
}
```

This simplifies the type hierarchy and eliminates the practice often needed in C# and Java of defining a matching interface for most classes. The feature also necessitates several of Adamant's other unique features. Members declared private are accessible only from the same instance, not by other instances of the same class. Public fields can be overridden by properties in subclasses, so there is no need to declare properties for every field. For more information see [Interfaces](traits.md).

### Immutable by Default

All variable declarations and types are immutable by default. Mutability has to be opted into.

### Guaranteed Optimizations

Because of immutable by default and other features of the language, it becomes possible to safely execute some code at compile time. For example, if you initialize a constant to the square root of two, this computation will be performed during compilation and the constant will be directly initialized with the resulting value.

Exactly how this feature will work is still being determined.

### Asynchronous Everywhere

Adamant will have C# and JavaScript style async/await support. However, in those languages, async becomes contagious and must be passed up the call chain throughout your code. Because of pervasive support for async execution, that is not the case in Adamant. Instead, it is safe to await a task in synchronous code and it will block the current thread of execution without holding a thread.

In C#, asynchronous code can easily lead to race conditions as two threads share data across an asynchronous boundary. In Adamant, the borrow checker protects the developer from any such possible race conditions so that asynchronous code is fully safe.

### Generics with Partial Specialization

Generics as supported by C# and Java are much easier to reason about than C++ style templates. However, they can be more restrictive. The type system of Adamant allows for much greater flexibility in generic types. For example, it is possible to use the type `void` as a type parameter to a generic class or function. Additionally, it is possible to provide specialized implementations of a generic class or function for specific types for better performance.

### Open Methods

The fragile base class problem occurs when a class overrides a base classes methods. This is avoided by specifically marking methods which the base class will call subclass implementations with the `open` keyword.

### Named Constructors

Constructors can be given names to indicate their purpose and meaning.

### Accept Operator

The `..` operator is the accept operator used to accept visitors following the visitor pattern or function pointers whose first argument is the value to the left of the accept operator.

## Uncommon Features

This section lists features Adamant shares with other languages that are less common, but still contribute to the distinctive flavor of the Adamant language.

* Type Inference on Local variable declarations
* Diverging Functions
* A Specific Infinite Loop Keyword
* All for loops are iterator based
* Iterator performance often optimizes down to a C style for loop
* Operator Overloading
* Object Literals - allows creation of single instance of anonymous type
* Classes can be extended with additional methods in separate libraries
* Partial Classes - supports code generation
* Both reference and value types
* C# style generators with `yield` keyword
* `unsafe` code blocks and low level language features like raw pointers
* C interop

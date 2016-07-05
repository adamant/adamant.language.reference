# Ownership

The ownership system in Adamant is used to manage all resources.  Not just memory, but files, network sockets and any other resources.  This provides a great deal of safety to Adamant code.  At the same time, ownership is one of the most unique aspects of Adamant.  Like anything new and unfamiliar it can take a while to understand and learn (even if you're familiar with ownership in Rust, you'll find Adamant to be somewhat different).  Once you do though, you'll appreciate the significant benefits it brings and find that it doesn't get in your way, but rather serves as a useful aid in thinking about your code.  The ownership system of Adamant is designed to make the common cases clear and simple.

First, we'll look at the history of memory management to get some background and lay the foundation.  

## Background and History

To really understand the ownership model of Adamant, it is necessary to understand some technical details and helpful to understand the historical context.  If you are familiar with the material covered by one of these sections, feel free to skim it.

### Stack and Heap

At the very beginning of programming, programs often placed each value in a fixed location in memory.  There was no dynamic memory and no true local variables.  It didn't take long for that to change.

As you're likely aware, in modern programming the computer memory is typically divided into two uses.  The first is the stack where local variables of functions are stored.  It's called the stack because each function call pushes a *stack frame* data structure that holds information relevant to that function call onto the stack.  When a function returns, that stack frame is popped off the stack.  Values on the stack exist as long as function call they are for is outstanding.  That existence is defined by the lexical scope of the variable.  Management of the stack is entirely automatic, deterministic and safe.

Some values need to exist for spans of time that don't correspond to any particular function call.  For example, data might be loaded when the user opens a file, and deleted when the user closes the file.  To support these scenarios, the heap is used.  The heap is a large, relatively unorganized area of memory that any time a new value is needed, an empty space is found and set aside for that value.  When the memory is no longer needed, that spaced is freed to be used for some future value.  Management of the heap is not automatic, often not deterministic and potentially unsafe.  It is the use and existence of the heap that has been one of the major concerns in computer programming.

### Manual Memory Management

The first approach to managing the heap was entirely manual.  When memory was needed, the programmer requested it using a function like `malloc()` and when the memory was no longer needed it was freed with `free()`.  This approach was error prone leading to many potential issues, such as:

  * Use After Free: Using some memory even though free had already been called on it.
  * Memory Leaks: Never calling free on some memory even though it is no longer used. Leads to running out of memory.
  * Dereferencing Null or Invalid Pointers: Accessing memory at address zero or simply at an invalid address.
  * Freeing Invalid Pointers: Freeing the same block of memory more than once or simply attempting to call free on an invalid pointer.
  * Use of Uninitialized Memory: Accessing memory that has been allocated before anything has been written to it, producing essentially random data leading to incorrect behavior.
  * Accessing Memory out of Bounds: Reading or writing past the end of a block of memory that was allocated.

Some of these pitfalls have been addressed in various languages with improved type systems.  However, the major issues of potential use after free, memory leaks and invalid pointers are essentially impossible to entirely prevent when using manual memory management.
  
### Garbage Collection

Currently, garbage collected languages have largely supplanted languages with manual memory management for high level programming tasks.  The garbage collector runs in the background, searching through allocated memory and determining what memory can no longer be reached and consequently, is no longer used.  Then it frees that memory.  Languages like C#, Java and many others with garbage collectors have totally eliminated almost the entire list of issues with manual memory management.  It is still possible to leak memory by unintentionally keeping a reference to it, but that is rarer than failing to call `free()` was.  It is also still possible to dereference a null pointer, but some modern languages have even corrected that.

Even though garbage collection has been a great boon to computer programming it is not without its issues. The collector running in the background can lead to unpredictable pauses or slowdowns (newer designs have gotten better in the regard).  When memory will actually be freed is non-deterministic.  That can cause virtual memory thrashing if memory is near full because the garbage collector has yet to free memory.  Very importantly, the non-determinism means the garbage collector is not a suitable means for managing resources besides memory like files and network sockets.  While the garbage collector is often used as a backstop for freeing such resources, the programmer really should release them as soon as they are no longer needed.  Garbage collectors are also complex compute intensive programs in their own right.

### Other Innovations

At the same time that languages and programmers have been relying more and more on garbage collection, programmers in languages with manual memory management have been developing techniques to improve its safety.

In languages like C++, *smart pointers* are widely used.  Smart pointers come in a number of forms but have the effect of declaring in the type system how some piece of memory should be managed.  The language is then able to enforce deterministic safe memory management for that block of memory.  Smart pointers are generally some form of unique or reference counted pointer.  A unique pointer ensures that it is the only reference to a given piece of memory.  When the pointer goes out of scope, the memory will be free.  The unique pointer can be explicitly moved to allow the memory to be used and live outside of its originating function.  Reference counted pointers keep a count of how many references there are to a given block of memory and free it when the last reference is deleted.  Reference counting can lead to situations where a *cycle* (a circular chain of references) will never be freed, leading to a memory leak.  To address this, there are also *weak pointers* that reference a counted block of memory without increasing the reference count.  These provide safety by forcing the program to check if the object has been deleted before accessing it.   Reference counting in the presence of multiple threads requires locking and can significantly impact performance.

The C++ language also originated the idea of Resource Acquisition is Initialization (RAII).  RAII is the idea that acquiring a resource like a file or network socket should be tied to the lifetime of an object.  If that object is on the stack or referenced by a smart pointer, that makes the acquisition and release of the resource deterministic and safe.  The management of resources besides memory using the same techniques as memory is a great boon.  In ability to use RAII is one of the downfalls of garbage collection.

### Innovation in Languages

Several languages have taken the innovations of smart pointers and RAII and baked them into the language.

First with Objective-C and the in Swift, Apple has provided "Automatic Reference Counting" (ARC).  ARC makes reference counting the default ubiquitous way of managing all references.  It builds into the language the bookkeeping of incrementing and decrementing reference counts.  Because it is built in, the language can avoid unnecessary modifications of the reference count.  It also makes RAII the default approach for managing resources.

The real star of innovation in the space of memory management in languages is Rust.  Rust introduces an ownership model in which all memory is deterministically, safely and mostly automatically managed. RAII is then used to safely manage all other resources.  The ownership model also provides the basis of safety around concurrency in Rust.  Adamant takes its primary inspiration from Rust when it comes to managing memory, resources and concurrency.  However,  Rust is designed as a fairly low level language with zero cost abstractions.  The result is that the programmer is frequently forced to think about and keep track of issues related to memory management.  Adamant re-imagines reworks the ownership model to bring it to a high level language that will feel familiar to developers who have worked in languages like C# or Java.
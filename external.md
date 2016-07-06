# External

The `external` keyword declares blocks  of functions, structs and global variables that are implemented externally.  Use compiler attributes and compiler settings to control this.

## External Structs

Structs declared external follow the layout rules of the target language.

## External Function Example

All parameters and returns from C external functions must be external structs or pointers or references them.

	@link("libc") // Less generic attribute name
	external // by default, changes to 'C' ABI
	{
		public Function() -> void;
	}
	
## Exporting Example

	@export // tells it to make this function visible externally, and not mangle the name, by default, changes to 'C' ABI
	public Something() -> void
	{
	}
	
	@export("NewName") // export as the given name
	@calling_convention(fast) // should take an enum value, can also be applied to external block
	public Something2() -> void
	{
	}
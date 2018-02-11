# Paths

To support paths cross platform, Adamant uses special path objects to represent paths. This ensures that any path from any platform can be safely and correctly represented. It also means that any character can be supported in a file name without some weired scheme of escaping the path separator. For example, if there were a platform that allows '/' in file names, that wouldn't be a problem. There are then methods for converting from and to the platform native path string and a property for what the native path operator is.

## Path Combination
The `/` operator is defined on paths to mean path combination.
# Escaped Identifiers

The backtick is used to escape keywords and reserved words for use as identifiers.  For example `` `keyword``.

For more flexibility in identifiers, double backtick could allow arbitrary characters (except double backtick).  As an example, ` ``This is an identifier with spaces in it!`` `.

Backtick may also be a convenient way to mark other literals. For example:

  * Date times - `` `2015-03-03``
  * Intervals - `` `[3..6)`` or `` `[3,6)``

Perhaps backtick could be used for operator overloading?  So `` `||`` would be the method name for overloading operator '`||`'  However, that might be confusing.
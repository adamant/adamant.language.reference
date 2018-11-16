# Localization

For localization, there are a number of locales that might be in play. They are:

* Display Locale - The locale to use for all formatting/output a user may see. The default value is controlled by the target locale.
* User Locale - The locale as configured by the current user. (For web servers etc. this is the server's locale settings)
* Invariant Locale - A locale whose output is uniform across all systems and times.
* Specific Locales - Locale objects can be created for specific locales with or without user overrides.

Many applications are developed without regard for internalization. For these applications the concept of *target locale* exists. The target locale determines the default for the current locale. Target locale can be set to one of three options:

* User Locale - The app is meant to be fully localized, current culture defaults to the user's culture
* Specific Locale *with* User Overrides - The default locale is the specified locale with user settings overriding things like date formatting.
* Specific Locale *without* User Overrides - The default locale is the specified locale without any user overrides. This then allows the setting of specific formatting etc. In this case, the locale is fully determined at compile time.

**The target locale defaults to en-US *with* user overrides.**

Note: .NET uses the term "culture", but "locale" seems to be both the more standard term and more precise.

## Debug Formatting

When converting a value to a string for debug purposes the display locale is used by default. However, there should be a way to override this for a developer wishing to test the app in a separate locale from their testing. That probably means there is a debug display locale.

## Conventions

**TODO:** Would it be better to name these methods "format" as the opposite of parse instead of "to string"?

* `to_display_string()`
* `to_display_string(format: string)`
* `to_debug_string()`
* `to_debug_string(format: string)`
* `to_invariant_string()`
* `to_invariant_string(format: string)`
* `to_string(locale: Locale)`
* `to_string(locale: Locale, format: string)`

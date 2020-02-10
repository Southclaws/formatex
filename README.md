# formatex

[![sampctl](https://shields.southcla.ws/badge/sampctl-formatex-2f2f2f.svg?style=for-the-badge)](https://github.com/Southclaws/formatex)

Slice's formatex because it doesn't have a GitHub repo.

Original thread: http://forum.sa-mp.com/showthread.php?t=313488

All credits to [Slice/oscar-broman](https://github.com/oscar-broman)

The original library wasn't shipped with an explicit license but I'm sure, in
the spirit of Open Source, Slice is happy to have this redistributed via
GitHub - if not, let me know!

## Installation

Simply install to your project:

```bash
sampctl package install Southclaws/formatex
```

Include in your code and begin using the library:

```pawn
#include <formatex>
```

---

## Original Documentation

Hey,

After seeing a great new feature in sscanf that allows you to create custom
specifiers, I figured it was only right to do the same to format and printf!

For example, the following code could be made a lot shorter:

```pawn
// This will put into "msg" the weapon you were given by a player, and the color of the player's name will match the player's color.

new msg[128], name[MAX_PLAYER_NAME], color;

color = GetPlayerColor(playerid);

GetPlayerName(playerid, name, sizeof(name));

format(msg, sizeof(msg), "You were given a %s by {%06x}%s", g_WeaponNameArray[weapon], color >>> 8, name);
```

This will do **the same thing**:

```pawn
new msg[128];

format(msg, _, "You were given %w by %P", weapon, playerid);

// msg is for example: You were given an M4 by Slice
```

## New Specifiers

| Specifier | Description                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------ |
| %p        | Name of the player ID given.                                                                                 |
| %P        | Name of the player ID given, with the player's color before it.                                              |
| %C        | Inline color (ex. {FFFFFF}) from a normal color (ex. colors you get from GetPlayerColor).                    |
| %v        | Vehicle model name from the model given (not vehicle ID, vehicle model).                                     |
| %w        | Weapon name, lower-case singular (to be used in sentences). Example: "an M4", "a combat shotgun", "a knife". |
| %W        | Weapon's name.                                                                                               |
| %X        | 8-byte, unsigned hex string (ex. FFFFFFFF).                                                                  |
| %u        | Unsigned integer.                                                                                            |

## You can create your own specifiers!

You can do this very easily; example of one that puts an upper-case string at
%S: Add this anywhere outside of a function:

```pawn
// Upper-case string
FormatSpecifier<'S'>(output[], const param[]) {
	for (new i = 0, l = min(sizeof(output), strlen(param)); i < l; i++)
		output[i] = toupper(param[i]);
}
```

You can now do this:

```pawn
printf("hello %S!", "world");

// prints: hello WORLD!
```

If you want an integer, float, or anything else instead of a string you just
change it:

```pawn
// You can call the argument whatever you like, and it can be a string/array:
FormatSpecifier<'A'>(output[], Float:health) { ... }
FormatSpecifier<'B'>(output[], objectid) { ... }
FormatSpecifier<'C'>(output[], playerid) { ... }
FormatSpecifier<'D'>(output[], Text:td) { ... }
FormatSpecifier<'D'>(output[], const string[]) { ... }
```

## Custom fallback values
formatex allows you to customize the fallback return value of the following specifiers; `%P`, `%p`, `%W`, `%w`, and `%v`:

```pawn
#define FORMAT_FALLBACK_P "No player found"
#define FORMAT_FALLBACK_p "{ABCDEF}void!!!"
#define FORMAT_FALLBACK_W "Tis aint murica"
#define FORMAT_FALLBACK_w "Ammunation"
#define FORMAT_FALLBACK_v "Vrum Vrum much?"

#include "formatex.inc"

main() {

	// Passing an invalid playerid
	printf("%%P: %P", INVALID_PLAYER_ID);

	// Passing an invalid playerid
	printf("%%p: %p", -59);

	// Passing an invalid weaponid
	printf("%%W: %W", 9250);

	// Passing an invalid weaponid
	printf("%%w: %w", -560);

	// Passing an invalid vehicleid
	printf("%%v: %v", 10);
}
```

**Output:**
```plaintext
%P: No player found
%p: {ABCDEF}void!!!
%W: Tis aint murica
%w: Ammunation
%v: Vrum Vrum much?
```

## Additional notes

Worth mentioning is this is a superset of format, meaning it has exactly all the
features of format and those behave like always (the only exception is it's a
bit friendlier to packed strings, though %s still doesn't support it).

It actually uses the original native "format" function to do the heavy lifting.

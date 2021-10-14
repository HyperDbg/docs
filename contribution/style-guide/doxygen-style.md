---
description: >-
  This guide introduces a consistent style for documenting HypedDbg source code
  using Doxygen
---

# Doxygen Style

This guide introduces a consistent style for documenting HyperDbg using [Doxygen](http://www.doxygen.org).

If you want to create a pull request and contribute in HyperDbg, please follow the Doxygen guides in the source code.

We follow the Javadoc syntax to mark comment blocks.

These have the general form for functions, structures, enums, global variables:

```c
/**
 * Brief summary.
 *
 * Detailed description. More detail.
 * @see Some reference
 *
 * @param <name> Parameter description.
 * @return Return value description.
 */
```

#### Example:

```c
/**
 * @brief Power function in order to computer address for MSR bitmaps
 * 
 * @param Base Base for power function
 * @param Exp Exponent for power function
 * @return int Returns the result of power function
 */
int
MathPower(int Base, int Exp)
{
...
}
```

### Doxygen Tags

This is the allowed set of Doxygen tags that can be used.

* [@brief](http://www.doxygen.nl/manual/commands.html#cmdbrief):[ ](http://www.doxygen.nl/manual/commands.html#cmdbrief)Starts a paragraph that serves as a brief description.
* [@details](http://www.doxygen.nl/manual/commands.html#cmddetails): Just like \[@brief]\(http://www.doxygen.nl/manual/commands.html#cmdbrief) starts a brief description, `\details` starts the detailed description.
* [@param](http://doxygen.org/manual/commands.html#cmdparam): Describes function parameters.
* \[@return]\(x) Describes return values.
* \[@see]\(http://doxygen.org/manual/commands.html#cmdsa) Describes a cross-reference to classes, functions, methods, variables, files, or URL.
* \[@warning]\(http://www.doxygen.nl/manual/commands.html#cmdwarning) Starts a paragraph where one or more warning messages may be entered.

Also, don't forget to add your name on top of the file with `@author` tag.

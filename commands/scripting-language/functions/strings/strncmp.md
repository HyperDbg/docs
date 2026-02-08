---
description: Description of the 'strncmp' function in HyperDbg Scripts
---

# strncmp

### Function

> strncmp

### Syntax

> strncmp( Str1, Str2, Num );

### Parameters

**\[Str1]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Str2**.

**\[Str2]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Str1**.

**\[Num]**

Maximum number of characters to compare.

### Description

Compares up to _**Num**_ characters of the string _**Str1**_ to the string _**Str2**_.

If the characters are equal, the comparison continues with the next pair of characters. This process repeats until the characters differ, a terminating null character is encountered, or _**Num**_ characters have been compared in both strings, whichever occurs first.

### Return value

Returns an integer value indicating the relationship between the strings:<br>

| Return Value | Indicates                                                                                     |
| ------------ | --------------------------------------------------------------------------------------------- |
| <0           | The first character that does not match has a lower value in _**Str1**_ than in _**Str2**._   |
| 0            | The contents of both strings are equal.                                                       |
| >0           | The first character that does not match has a greater value in _**Str1**_ than in _**Str2**._ |

### Examples

``strncmp(fffff806`6de00000, @rax+c0, 0n12);``

Compare the the first 12 (decimal) characters of the string located at ``fffff806`6de00000`` with the string located at `@rax+c0`.

`strncmp(@rcx, "Test String", b);`

Compare the first 0xb (hexadecimal) characters of the string located at `@rcx` with the string `"Test String"`.

`strncmp("Test String1", "Test String2", 0n7);`

Compare the first 7 (decimal) characters of the string `"Test String1"` with the string `"Test String2"`.

### Remarks

This function checks for the validity of both the source and the destination address before the comparison.&#x20;

The support for this function is added from **v0.9**.

### Related

[wcscmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcscmp)

[wcsncmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcsncmp)

[memcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcmp)

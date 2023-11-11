---
description: Description of the 'strcmp' function in HyperDbg Scripts
---

# strcmp

### Function

> strcmp

### Syntax

> strcmp( Str1, Str2 );

### Parameters

**\[Str1]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Str2**.

**\[Str2]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Str1**.

### Description

Compares the string _**Str1**_ to the string _**Str2**_.

### Return value

Returns an integer value indicating the relationship between the strings:\


| Return Value | Indicates                                                                                     |
| ------------ | --------------------------------------------------------------------------------------------- |
| <0           | The first character that does not match has a lower value in _**Str1**_ than in _**Str2**._   |
| 0            | The contents of both strings are equal.                                                       |
| >0           | The first character that does not match has a greater value in _**Str1**_ than in _**Str2**._ |

### Examples

``strcmp(fffff806`6de00000, @rax+c0);``

Compare the string located at ``fffff806`6de00000`` with the string located at `@rax+c0`.

`strcmp(@rcx, "Test String");`

Compare the string located at `@rcx` with the string `"Test String"`.

`strcmp("Test String1", "Test String2");`

Compare the string `"Test String1"` with the string `"Test String2"`.

### Remarks

This function checks for the validity of both the source and the destination address before the comparison.&#x20;

The support for this function is added from **v0.7**.

### Related

[wcscmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcscmp)

[memcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcmp)

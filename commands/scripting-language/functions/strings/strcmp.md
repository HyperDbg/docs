---
description: Description of 'strcmp' function in HyperDbg Scripts
---

# strcmp

### Function

> strcmp

### Syntax

> strcmp( Expression, Expression );

### Parameters

**\[Expression (Str1)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the first target location or target string to perform the comparison.

**\[Expression (Str2)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the first target location or target string to perform the comparison.

### Description

Compares the C string **Str1** to the C string **Str2**.

### Return value

Returns an integral value indicating the relationship between the strings:\


| return value | indicates                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------- |
| `<0`         | the first character that does not match has a lower value in _**Str1**_ than in _**Str2**_   |
| `0`          | the contents of both strings are equal                                                       |
| `>0`         | the first character that does not match has a greater value in _**Str1**_ than in _**Str2**_ |

### Examples



### Remarks

None

### Related

[strlen](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strlen)

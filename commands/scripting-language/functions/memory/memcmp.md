---
description: Description of the 'memcmp' function in HyperDbg Scripts
---

# memcmp

### Function

> memcmp

### Syntax

> memcmp( Ptr1, Ptr2, Num );

### Parameters

**\[Ptr1]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Ptr2**.

**\[Ptr2]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to compare with **Ptr1**.

**\[Num]**

Number of bytes to compare.

### Description

Compares the first _**Num**_ bytes of the block of memory pointed by _**Ptr1**_ to the first _num_ bytes pointed by _**Ptr2**._

### Return value

Returns an integer value indicating the relationship between the content of the memory blocks:

| return value | indicates                                                                                                                                                   |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <0           | The first byte that does not match in both memory blocks has a lower value in _**Ptr1**_ than in _**Ptr2**_ (if evaluated as _**unsigned char**_ values).   |
| 0            | The contents of both memory blocks are equal.                                                                                                               |
| >0           | The first byte that does not match in both memory blocks has a greater value in _**Ptr1**_ than in _**Ptr2**_ (if evaluated as _**unsigned char**_ values). |

### Examples

``memcmp(fffff806`6de00000, @rax+c0, 5);``

Compare the first **5** bytes of the memory located at ``fffff806`6de00000`` with the memory located at `@rax+c0`.

`memcmp(@rcx, "Test1", 5);`

Compare the first 5 bytes of the memory located at `@rcx` with the string `"Test1"`.

### Remarks

This function checks for the validity of both the source and the destination address before the comparison.&#x20;

The support for this function is added from **v0.7**.

### Related

[strcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strcmp)

[wcscmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcscmp)

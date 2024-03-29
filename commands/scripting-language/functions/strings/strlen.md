---
description: Description of the 'strlen' function in HyperDbg Scripts
---

# strlen

### Function

> strlen

### Syntax

> strlen( Expression );

### Parameters

**\[Expression (Address)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a string to evaluate which is the address of the target location to compute the string length.

### Description

Counts the length of the ASCII string from an address.

### Return value

The return value is the length of the strings which is located at the target address. If there is an error or if the address is invalid then, it returns `0`.

### Examples

`StringLen = strlen(@rcx + 10);`

Adds 0x10 to the **rcx** register and counts the length of the ASCII string, which is determined by the terminating null-character and saves the result into the `StringLen` variable.

`StringLen = strlen(dq(@rcx));`

Counts the ASCII string length as an 8-byte hex address, pointed by the **rcx** register, and saves the result into the `StringLen` variable.

`StringLen = strlen(0xffff83811f265040);`

Counts the ASCII string length located at `0xffff83811f265040` address and saves the result into the `StringLen` variable.

`StringLen = strlen("ABCD");`

Counts ASCII the string length of the "**ABCD**" string and saves the result into the `StringLen` variable.

### Remarks

None

### Related

[wcslen](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcslen)

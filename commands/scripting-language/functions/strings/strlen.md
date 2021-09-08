---
description: Description of 'strlen' function in HyperDbg Scripts
---

# strlen

### Function

> strlen

### Syntax

> strlen\( Expression \);

### Parameters

**\[Expression\]**

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the target location to compute the string length.

### Description

Counts the length of the ASCII string from an address.

### Return value

The return value is the length of the strings which is located at the target address. If there is an error or if the address is invalid then, it returns `0`.

### Examples

`StringLen = strlen(@rcx + 10);`

Adds 0x10 to the **rcx** register and counts the length of the ASCII string, which is determined by the terminating null-character and saves the result into `StringLen` variable.

`StringLen = strlen(dq(@rcx));`

Counts ASCII string length as an 8-byte hex address, pointed by **rcx** register and saves the result into `StringLen` variable.

`StringLen = strlen(0xffff83811f265040);`

Counts ASCII string length located at `0xffff83811f265040` address and saves the result into `StringLen` variable.

### **Remarks**

None


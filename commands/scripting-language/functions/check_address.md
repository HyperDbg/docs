---
description: Description of 'check_address' function in HyperDbg Scripts
---

# check\_address

### Function

> check\_address

### Syntax

> check\_address\( Expression \);

### Parameters

**\[Expression\]**

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the target location to check.

### Description

Checks an address to see if the address is **valid** and **safe to access** from or not.

### Return value

If the address is valid and safe it returns `1`. If the address is invalid or not safe to access then it returns `0`.

### Examples

`check_address(@rcx + 10);`

Adds 0x10 to the **rcx** register and checks whether it's a valid and safe address or not.

`check_address(dq(@rcx));`

Checks address as an 8-byte hex, pointed by **rcx** register.

`check_address(0xffff83811f265040);`

Checks `0xffff83811f265040` address for validity and safety.

### **Remarks**

None




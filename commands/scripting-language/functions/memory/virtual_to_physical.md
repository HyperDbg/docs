---
description: Description of 'virtual_to_physical' function in HyperDbg Scripts
---

# virtual\_to\_physical

### Function

> virtual\_to\_physical

### Syntax

> virtual\_to\_physical( Expression );

### Parameters

**\[Expression (Virtual Address)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate the target virtual address that will be converted to the physical address.

### Description

Converts virtual address to physical address based on the memory layout of the currently running process.

### Return value

If the address is not valid, it returns `0`. Otherwise, it returns the **physical address** of the target virtual address.

### Examples

`PhysAddr = virtual_to_physical(@rip + 10);`

Adds 0x10 to the **rip** register and convert it to the physical address and save the physical address to `PhysAddr`.

### Remarks

None

### Related

None

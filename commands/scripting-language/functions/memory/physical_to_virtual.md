---
description: Description of 'physical_to_virtual' function in HyperDbg Scripts
---

# physical\_to\_virtual

{% hint style="danger" %}
This function is not working. **PLEASE DO NOT USE IT FOR NOW**. It will be fixed in future versions.
{% endhint %}

### Function

> physical\_to\_virtual

### Syntax

> physical\_to\_virtual( Expression );

### Parameters

**\[Expression (Physical Address)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate the target physical address that will be converted to the virtual address.

### Description

Converts physical address to virtual address based on the memory layout of the currently running process.

### Return value

If the address is not valid, it returns `0`. Otherwise, it returns the **virtual address** of the target physical address.

### Examples

`VirtAddr =` physical\_to\_virtual`(@rdx+ 10);`

Adds 0x10 to the **rdx** register and convert it to the virtual address and save the virtual address to `VirtAddr`.

### Remarks

None

### Related

None

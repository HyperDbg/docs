---
description: Description of the 'memcpy_pa' function in HyperDbg Scripts
---

# memcpy\_pa

### Function

> memcpy\_pa

### Syntax

> memcpy\_pa( PhysicalDestination, PhysicalSource, Num );

### Parameters

**\[PhysicalDestination]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the physical address of the **destination** (physical) buffer.

**\[PhysicalSource]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the physical address of the **source** buffer.

**\[Num]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the size (length) of the bytes to be copied.

### Description

Copies the contents of the source physical address to the destination (physical) buffer.

### Return value

None

### Examples

`memcpy_pa(6de00000 , @rax, 40);`

Copies **0x40** bytes from the physical buffer pointed by the **RAX** register to the buffer which its physical address is `6de00000`.

### Remarks

Please note that the size argument is passed in the **HEX** format.

Make sure to acquire a spinlock if the destination buffer might be accessed by different cores simultaneously.

This function checks for the validity of both the source and the destination address before performing the copy.&#x20;

The support for this function is added from **v0.13**.

### Related

[memcpy](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcpy)

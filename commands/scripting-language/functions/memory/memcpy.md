---
description: Description of the 'memcpy' function in HyperDbg Scripts
---

# memcpy

### Function

> memcpy

### Syntax

> memcpy( Destination, Source, Num );

### Parameters

**\[Destination]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the **destination** buffer.

**\[Source]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the **source** buffer.

**\[Num]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the size (length) of the bytes to be copied.

### Description

Copies the contents of the source address to the destination buffer.

### Return value

None

### Examples

``memcpy(fffff806`6de00000 , @rax, 40);``

Copies **0x40** bytes from the buffer pointed by the **RAX** register to the buffer which its address is ``fffff806`6de00000``.

`memcpy($buffer, @rsp, 16);`

Copies **0x16** bytes from the stack to the buffer allocated by the event buffer.

### Remarks

Please note that the size argument is passed in the **HEX** format.

Make sure to acquire a spinlock if the destination buffer might be accessed by different cores simultaneously.

This function checks for the validity of both the source and the destination address before performing the copy.&#x20;

The support for this function is added from **v0.2**.

### Related

[memcpy\_pa](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcpy_pa)

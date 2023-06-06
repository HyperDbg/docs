---
description: Description of 'disassemble_len' function in HyperDbg Scripts
---

# disassemble\_len

### Function

> disassemble\_len

### Syntax

> disassemble\_len( Expression );

### Parameters

**\[Expression (Address)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the target location to return instruction length.

### Description

Returns the **length** of the instruction (Length Disassembler Engine) at the target `Address`.

### Return value

If the address is valid, returns the length (byte) of one instruction at the target address; otherwise, it returns `0`.

### Examples

```clike
InstrLength = disassemble_len(@rip + 10);
```

Adds 0x10 to the **rip** register and returns the instruction length result into the `InstrLength` variable.

### Remarks

None

### Related

None

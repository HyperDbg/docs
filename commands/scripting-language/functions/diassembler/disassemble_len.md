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

Returns the **length** of the instruction (Length Disassembler Engine) at the target `Address` using a **64-bit assembler**.

### Return value

If the address is valid, returns the length (byte) of one instruction at the target address; otherwise, it returns `0`.

### Examples

```clike
InstrLength = disassemble_len(@rip + 10);
```

Adds 0x10 to the **rip** register and returns the instruction length result into the `InstrLength` variable.

### Remarks

This function visits the memory as a 64-bit assembler. You can use [disassemble\_len32](https://docs.hyperdbg.org/commands/scripting-language/functions/diassembler/disassemble\_len32) for the 32-bit version of this function.

### Related

[disassemble\_len32](https://docs.hyperdbg.org/commands/scripting-language/functions/diassembler/disassemble\_len32)

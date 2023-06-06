---
description: Description of 'disassemble_len32' function in HyperDbg Scripts
---

# disassemble\_len32

### Function

> disassemble\_len32

### Syntax

> disassemble\_len32( Expression );

### Parameters

**\[Expression (Address)]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the address of the target location to return instruction length.

### Description

Returns the **length** of the instruction (Length Disassembler Engine) at the target `Address` using a  **32-bit assembler**.

### Return value

If the address is valid, returns the length (byte) of one instruction at the target address; otherwise, it returns `0`.

### Examples

```clike
InstrLength = disassemble_len32(@eip + 10);
```

Adds 0x10 to the **eip** register and returns the instruction length result into the `InstrLength` variable.

### Remarks

This function visits the memory as a **32-bit assembler**. You can use [disassemble\_len](https://docs.hyperdbg.org/commands/scripting-language/functions/diassembler/disassemble\_len) for the 64-bit version of this function.

### Related

[disassemble\_len](https://docs.hyperdbg.org/commands/scripting-language/functions/diassembler/disassemble\_len)

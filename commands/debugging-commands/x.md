---
description: Description of 'x' command in HyperDbg.
---

# x \(examine symbols and find functions and variables address\)

### Command

> x

### Syntax

> p\[r\] \[count \(hex value\)\]

### Description

Executes a single instruction \(step-over\) and optionally displays the resulting values of all registers and flags.

### Parameters

**\[count\] \(optional\)**

          Count of step\(s\), or how many times perform the stepping operation. If you don't set this argument, then by default, the **count** is `1`.

### Examples

### IOCTL

None

### **Remarks**

This command will set a **trap flag** in debuggee and continue all the cores. After executing one instruction, it halts the debuggee again.

If the target instruction is a call instruction, it configures the first hardware debug register breakpoint to the instruction after that call. After that, it continues the debuggee and waits for the call to be returned and the next instruction \(after the call\) to be executed.

All cores and threads \(except the currently executing thread\) find a chance to be executed between each step in this type of stepping.

### Requirements

None

### Related

None


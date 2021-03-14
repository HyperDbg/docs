---
description: Description of 'be' command in HyperDbg.
---

# be \(enable breakpoints\)

### Command

> be

### Syntax

> be \[breakpoint id \(hex value\)\]

### Description

Enables a previously disabled breakpoint \(**0xcc**\).

### Parameters

**\[breakpoint id \(hex value\)\]**

          The **breakpoint id** of the target breakpoint. You can see a list of breakpoints and breakpoint ids using the '[bl](https://docs.hyperdbg.com/commands/debugging-commands/bl)' command.

### Examples

_Todo_

### IOCTL

_Todo_

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[bp \(set breakpoint\)](https://docs.hyperdbg.com/commands/debugging-commands/bp)

[bl \(list breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bl)

[bd \(disable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bd)

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bc)


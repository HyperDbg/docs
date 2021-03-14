---
description: Description of 'bl' command in HyperDbg.
---

# bl \(list breakpoints\)

### Command

> bl

### Syntax

> bl

### Description

Lists all the enabled/disabled breakpoints.

### Parameters

None

### Examples

The following command shows how you can use the 'bl' command.

```text
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  enabled
03   fffff801639b103a  enabled
04   fffff801639b103f  enabled
```

### IOCTL

_Todo_

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[bp \(set breakpoint\)](https://docs.hyperdbg.com/commands/debugging-commands/bp)

[be \(enable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/be)

[bd \(disable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bd)

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bc)


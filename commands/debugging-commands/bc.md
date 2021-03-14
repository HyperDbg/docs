---
description: Description of 'bc' command in HyperDbg.
---

# bc \(clear and remove breakpoints\)

### Command

> bc

### Syntax

> bc \[breakpoint id \(hex value\)\]

### Description

Clears and removes a breakpoint \(**0xcc**\).

### Parameters

**\[breakpoint id \(hex value\)\]**

          The **breakpoint id** of the target breakpoint. You can see a list of breakpoints and breakpoint ids using the '[bl](https://docs.hyperdbg.com/commands/debugging-commands/bl)' command.

### Examples

Imagine we have the following active breakpoints.

```text
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  enabled
03   fffff801639b103a  enabled
04   fffff801639b103f  enabled
```

After executing the following command, it's now removed.

```text
HyperDbg> bc 2
```

If we see the list of active breakpoints again, you can see that it's removed.

```text
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
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

[bl \(list breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bl)

[be \(enable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/be)

[bd \(disable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bd)


---
description: Description of 'bd' command in HyperDbg.
---

# bd \(disable breakpoints\)

### Command

> bd

### Syntax

> bd \[breakpoint id \(hex value\)\]

### Description

Disables a previously enabled breakpoint \(**0xcc**\).

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

After executing the following command, it's now disabled.

```text
HyperDbg> bd 2
```

If we see the list of active breakpoints again, you can see that it's disabled.

```text
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  disabled
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

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bc)


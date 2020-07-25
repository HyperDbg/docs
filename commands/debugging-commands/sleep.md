---
description: Description of 'sleep' command in HyperDbg.
---

# sleep \(wait for specific time in the .script command\)

### Command

> sleep

### Syntax

> sleep \[time to wait \(milliseconds - hex value\)\]

### Description

Waits for the specified time \(in milliseconds\).

\[time to wait \(milliseconds - hex value\)\]

          The time that debugger should wait, in milliseconds.

### IOCTL

None

### **Remarks**

This command is used in batch script files when we want to gather information for a specific time and then executing [**pause** ](https://docs.hyperdbg.com/commands/debugging-commands/pause)command or [**unload** ](https://docs.hyperdbg.com/commands/debugging-commands/unload)command. 

### Requirements

None

### Related

[pause \(break to the debugger and pause processing kernel packets\)](https://docs.hyperdbg.com/commands/debugging-commands/pause)

[unload \(unload the kernel modules\)](https://docs.hyperdbg.com/commands/debugging-commands/unload)

[.script \(run batch script commands\)](https://docs.hyperdbg.com/commands/meta-commands/.script)


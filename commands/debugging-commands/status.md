---
description: Description of 'status' command in HyperDbg.
---

# status \(show the debuggee status\)

### Command

> status

### Syntax

> status

### Description

Shows the connection status of the **HyperDbg**'s Connection.

### Parameters

None

### Examples

The following example shows the status of the **debuggee**.

```text
HyperDbg (192.168.1.10:50000)> status
local debugging ('vmi mode')
```

### IOCTL

None

### **Remarks**

In **HyperDbg**, 'status' and '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' commands are different if you connected to a remote debugger, but if you try to debug your system locally, then these commands are the same.

If you execute the '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' command, it shows the current debugger's status \(the debuggee's IP address\). If you execute the '**status**' command, it will be executed in the debuggee and shows the debuggee's state \(not debugger\).

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

### Requirements

None

### Related

[.status \(show the debugger status\)](https://docs.hyperdbg.com/commands/meta-commands/.status)


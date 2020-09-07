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
HyperDbg (192.168.1.10:50000) >status
local debugging ('vmi mode')
```

### IOCTL

None

### **Remarks**

In **HyperDbg**, 'status' and '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' commands are different if you connected to a remote debugger but if you try to locally debug your system then these commands are the same.

If you execute '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' command, then it shows the status of the current debugger \(the IP address of debuggee\) and if you execute '**status**' command then it will be executed in the debuggee and shows the state of debuggee \(not debugger\).

### Requirements

None

### Related

[.status \(show the debugger status\)](https://docs.hyperdbg.com/commands/meta-commands/.status)


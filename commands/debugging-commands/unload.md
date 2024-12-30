---
description: Description of the 'unload' command in HyperDbg.
---

# unload (unload the kernel modules)

### Command

> unload

### Syntax

> unload \[remove] \[ModuleName (string)]

### Description

Unloads the HyperDbg drivers and kernel modules from the target system.

### Parameters

**\[remove]\(optional)**

If you want to remove the installed driver. (See **Remarks** for more information)

**\[ModuleName (string)]**

The name of the module that you want to unload.

## Modules

| Module Name | Description                     |
| ----------- | ------------------------------- |
| vmm         | Hypervisor-related capabilities |

{% hint style="info" %}
The debugger functions are implemented on top of the 'vmm' module.
{% endhint %}

**vmm** : this module contains commands related to the debugger and all hypervisor-related capabilities. Currently, **vmm** is the only module of **HyperDbg**.

### Examples

The following example unloads `vmm` module.

```
HyperDbg> unload vmm
```

### SDK

To unload the HyperDbg driver on the local machine, you need to use the following functions in `libhyperdbg`:

```clike
INT
hyperdbg_u_stop_vmm_driver();
```

Then,

```clike
INT
hyperdbg_u_unload_vmm();
```

Later you can uninstall the driver using the following SDK function:

```clike
INT
hyperdbg_u_uninstall_vmm_driver();
```

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

If you use the `remove` argument, then the driver will be marked to be stopped and uninstalled. You **cannot** re-load that module again until the target machine is restarted.

### Requirements

None

### Related

[load (load the kernel modules)](https://docs.hyperdbg.org/commands/debugging-commands/load)

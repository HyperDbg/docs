---
description: Description of 'unload' command in HyperDbg.
---

# unload \(unload the kernel modules\)

## Command

> unload

## Syntax

> unload \[module name\]

## Description

Unloads the HyperDbg drivers and kernel modules from the target system.

## Parameters

**\[remove\]\(optional\)**

If you want to remove the installed driver \(See **Remarks** for more information\)


**\[module name\]**

The name of the module that you want to unload

## Modules

| Module Name | Description |
| :--- | :--- |
| vmm | Hypervisor-related capabilities |

{% hint style="info" %}
The debugger functions are implemented on top of the 'vmm' module.
{% endhint %}

**vmm** : this module contains commands related to the debugger and all hypervisor-related capabilities. Currently, **vmm** is the only module of **HyperDbg**.

## Examples

The following example unloads `vmm` module.

```text
HyperDbg> unload vmm
```

## IOCTL

This function first invokes `IOCTL_TERMINATE_VMX` to turn off the vmx operation and `IOCTL_RETURN_IRP_PENDING_PACKETS_AND_DISALLOW_IOCTL` to complete all the IRP Pending sessions so that we can call `CloseHandle`.

If you're using APIs, the following export in **hprdbgctrl** can be used.

```text
HPRDBGCTRL_API int HyperdbgUnload();
```

## **Remarks**

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

If you use the **`remove`** argument, then the driver will be marked to be stopped and uninstalled. You **cannot** re-load that module again until the target machine is restarted.

## Requirements

None

## Related

[load \(load the kernel modules\)](https://docs.hyperdbg.org/commands/debugging-commands/load)


---
description: Description of 'unload' command in HyperDbg.
---

# unload \(unload the kernel modules\)

### Command

> unload

### Syntax

> unload \[module name\]

### Description

Unloads the HyperDbg drivers and kernel modules from the target system.

### Parameters

\[module name\]

          The name of the module that you want to unload

### Modules

| Module Name | Description |
| :--- | :--- |
| vmm | Hypervisor-related capabilities |

{% hint style="info" %}
The debugger functions are implemented on top of 'vmm' module.
{% endhint %}

**vmm** : this module contains commands related to debugger and all hypervisor-related capabilities. currently, **vmm** is the only module of **HyperDbg**.

### IOCTL

This function first, invokes `IOCTL_TERMINATE_VMX` to turn off the vmx operation and `IOCTL_RETURN_IRP_PENDING_PACKETS_AND_DISALLOW_IOCTL` to complete all the IRP Pending sessions so that we can call `CloseHandle`.

If you're using APIs, the following export in **hprdbgctrl** can be used.

```text
HPRDBGCTRL_API int HyperdbgUnload();
```

### **Remarks**

None

### Requirements

None

### Related

[load \(load the kernel modules\)](https://docs.hyperdbg.com/commands/debugging-commands/load)


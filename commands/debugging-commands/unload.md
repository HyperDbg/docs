---
description: Description of 'unload' command in HyperDbg.
---

# unload \(unload the kernel modules\)

### Command

> unload

### Syntax

> unload

### Description

Unloads the HyperDbg drivers and kernel modules from the target system.

### Parameters

None

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


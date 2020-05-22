---
description: Description of 'cpu' command in HyperDbg.
---

# load \(load the kernel modules\)

### Command

> load

### Syntax

> load

### Description

Loads the HyperDbg drivers and kernel modules into the target system.

### Parameters

None

### IOCTL

This command causes to run a `CreateFile` in target system, in `IRP_MJ_CREATE`, the driver loads the modules like vt-x and debugger and all other kernel modules, so it doesn't have an IOCTL, you can call `CreateFile` to the driver's device and get a handle.

If you're using APIs, the following export in **hprdbgctrl** can be used.

```text
HPRDBGCTRL_API int HyperdbgLoad();
```

### **Remarks**

1. Only one application can get the device handle, after that, no other application is able to create a handle from the device or in the other words is not able to call `load`command until the first app releases the handle \(by `CloseHandle`\) or call `unload` command. 
2. The application that requests to load the kernel modules should have `SeDebugPrivilege` to obtain a handle, otherwise an `ACCESS_DENIED` is thrown.

### Requirements

* Intel VT-x is required to be enabled to perform this action.
* Intel Extended Page Table \(a.k.a. SLAT\) should be present in the processor, if you have a Nehalem \(2008\) processor or newer processor than it supports it.

### Related

[unload \(unload the kernel modules\)](https://docs.hyperdbg.com/commands/debugging-commands/unload)


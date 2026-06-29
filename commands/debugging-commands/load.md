---
description: Description of the 'load' command in HyperDbg.
---

# load (load modules)

### Command

> load

### Syntax

> load \[ModuleName (string)]

### Description

Loads the HyperDbg's drivers and kernel modules into the target system.

### Parameters

**\[ModuleName (string)]**

The name of the module that you want to load.

## Modules

| Module Name | Description                                   |
| ----------- | --------------------------------------------- |
| all         | Loading all available modules                 |
| kd          | Kernel module (parent module for all modules) |
| vmm         | Hypervisor-related capabilities               |
| trace       | Tracing (Intel LBR, Intel PT) capabilities    |

{% hint style="info" %}
Most of the debugging functions are implemented on top of '**vmm**' module. If you load any of the above modules (**vmm**, **trace**, etc.), the **kd** module will be loaded automatically.
{% endhint %}

**KD**: This module contains certain debugging commands and is also the parent of all modules (manages and loads all other modules)

**VMM**: This module contains commands related to the debugger and all hypervisor-related capabilities.

**Trace**: This module contains commands for tracing functions such as Intel Processor Trace (PT) and Last Branch Record (LBR).

### Examples

The following example loads `vmm` module.

```
HyperDbg> load vmm
```

The following example loads `trace` module.

```
HyperDbg> load trace
```

The following example loads `all` modules.

```
HyperDbg> load all
```

The following example loads `kd` module.

```
HyperDbg> load kd
```

### SDK

To load the HyperDbg drivers on the local machine, you need to use the following function in `libhyperdbg` (depending on the module you want to load):

```c
INT

//
// All Modules
//
INT
hyperdbg_u_load_all_modules();

//
// VMM Module
//
INT
hyperdbg_u_load_vmm();

//
// KD (Kernel Debugger) Module
//
INT
hyperdbg_u_load_kd_module();

//
// HyperTrace Module
//
INT
hyperdbg_u_load_hypertrace_module();
```

Make sure to install drivers before loading the driver.

```c
INT
hyperdbg_u_install_kd_driver();
```

### Remarks

The application that requests to load the kernel modules should have `SeDebugPrivilege` to obtain a handle, otherwise an `ACCESS_DENIED` is thrown.

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

* Intel VT-x is required to be enabled to load the `vmm` module.
* For the `vmm` module, Intel Extended Page Table (a.k.a. SLAT) should be present in the processor. If you have a Nehalem (2008) processor or a newer processor, then it supports this feature.
* Intel PT and Intel LBR are required for loading the `trace` module.

### Related

[unload (unload the kernel modules)](https://docs.hyperdbg.org/commands/debugging-commands/unload)

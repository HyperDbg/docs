---
description: Description of the 'unload' command in HyperDbg.
---

# unload (unload modules)

### Command

> unload

### Syntax

> unload \[remove] \[ModuleName (string)]

### Description

Unloads the HyperDbg drivers and kernel modules from the target system.

### Parameters

**\[remove] (optional)**

If you want to remove the installed driver. (See **Remarks** for more information)

**\[ModuleName (string)]**

The name of the module that you want to unload.

## Modules

| Module Name | Description                                   |
| ----------- | --------------------------------------------- |
| all         | Loading all available modules                 |
| kd          | Kernel module (parent module for all modules) |
| vmm         | Hypervisor-related capabilities               |
| trace       | Tracing (Intel LBR, Intel PT) capabilities    |

### Examples

The following example unloads `vmm` module.

```
HyperDbg> unload vmm
```

The following example unloads `all` modules.

```
HyperDbg> unload all
```

The following example unloads `trace` module.

```
HyperDbg> unload trace
```

The following example unloads `kd` module.

```
HyperDbg> unload kd
```

### SDK

To unload the HyperDbg driver on the local machine, you need to use the following functions in `libhyperdbg` (depending on the module you want to load):

```c
//
// All Modules
//
INT
hyperdbg_u_unload_all_modules();

//
// VMM Module
//
INT
hyperdbg_u_unload_vmm();

//
// KD (Kernel Debugger) Module
//
INT
hyperdbg_u_unload_kd();

//
// HyperTrace Module
//
INT
hyperdbg_u_unload_hypertrace_module();
```

Later you can stop and uninstall (remove) the driver using the following SDK function:

```c
INT
hyperdbg_u_stop_kd_driver();

INT
hyperdbg_u_uninstall_kd_driver();
```

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[load (load the kernel modules)](https://docs.hyperdbg.org/commands/debugging-commands/load)

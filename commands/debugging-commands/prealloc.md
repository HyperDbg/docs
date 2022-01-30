---
description: Description of 'prealloc' command in HyperDbg.
---

# prealloc (reserve pre-allocated pools)

### Command

> prealloc

### Syntax

> prealloc \[type (monitor)] \[count (hex value)]

### Description

Reserves a specified number of pre-allocated pools.

In some cases, you need to reserve multiple pools to be used in vmx-root mode. This command will allocate these pools in **PASSIVE\_LEVEL**.

{% hint style="info" %}
HyperDbg tries to minimize the request for this command by allocating pools whenever your system is in **PASSIVE\_LEVEL**.
{% endhint %}

### Parameters

**\[type]**

The type of pools to be reserved

| Type                    |                                         |
| ----------------------- | --------------------------------------- |
| **monitor**             | for **!monitor** commands extra pages   |
| **thread-interception** | for processes with thousands of threads |

**\[count (hex value)]**

Number of pools to be allocated and reserved

### Examples

Imagine we want to pre-allocate and reserve **0x10** number of pools for the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command.

```
0: kHyperDbg> prealloc monitor 10
the requested pools are allocated and reserved
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_RESERVE_PRE_ALLOCATED_POOLS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_PREALLOC_COMMAND
{
    DEBUGGER_PREALLOC_COMMAND_TYPE Type;
    UINT64                         Count;
    UINT32                         KernelStatus;

} DEBUGGER_PREALLOC_COMMAND, *PDEBUGGER_PREALLOC_COMMAND;
```

You should only fill the **Type** and **Count** of the above structure when the IOCTL returns from the kernel, other parts of this structure are filled with appropriate **KernelStatus**.

The **Type** can be from the following enum:

```
typedef enum _DEBUGGER_PREALLOC_COMMAND_TYPE
{
    DEBUGGER_PREALLOC_COMMAND_TYPE_MONITOR,
    DEBUGGER_PREALLOC_COMMAND_TYPE_THREAD_INTERCEPTION,
} DEBUGGER_PREALLOC_COMMAND_TYPE;
```

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

None

---
description: Description of the 'prealloc' command in HyperDbg.
---

# prealloc (reserve pre-allocated pools)

### Command

> prealloc

### Syntax

> prealloc \[Type (string)] \[Count (hex)]

### Description

Reserves a specified number of pre-allocated pools. This command is mainly used to assist in the pre-allocation of pools for [**instant events**](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events).

In some cases, you need to reserve multiple pools to be used in vmx-root mode. This command will allocate these pools in **PASSIVE\_LEVEL**.

{% hint style="info" %}
HyperDbg tries to minimize the request for this command by allocating pools whenever your system is in **PASSIVE\_LEVEL**.
{% endhint %}

### Parameters

**\[Type (string)]**

The type of pool(s) to be reserved.

| Type                    |                                                                                                                                                                                                                                |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **thread-interception** | Used for storing details of processes with thousands of threads                                                                                                                                                                |
| **monitor**             | **!monitor** command's extra pages                                                                                                                                                                                             |
| **epthook**             | **!ephook** command's extra pages                                                                                                                                                                                              |
| **epthook2**            | **!ephook2** command's extra pages                                                                                                                                                                                             |
| **regular-event**       | Regular [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events)                                                                                                                                        |
| **big-event**           | Big [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events)                                                                                                                                            |
| **regular-safe-buffer** | Regular event safe buffers ([$buffer](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers)) for [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) |
| **big-safe-buffer**     | Big event safe buffers ([$buffer](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers)) for [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events)     |

**\[Count (hex)]**

Number of pool(s) to be allocated and reserved.

### Examples

Imagine we want to pre-allocate and reserve **0x10** number of pools for the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command. If we're operating in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), first we need to reserve and allocate pools for a [regular event](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events), then we need to allocate the same amount of pools for the memory **monitor** EPT hooks.

```
0: kHyperDbg> prealloc regular-event 10
the requested pools are allocated and reserved

0: kHyperDbg> prealloc monitor 10
the requested pools are allocated and reserved
```

Again if we want to pre-allocate and reserve **0x10** number of pools for the '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' command. If we're operating in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), first we need to reserve and allocate pools for a [regular event](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events), then we need to allocate the same amount of pools for the memory **epthook** EPT hooks.

```
0: kHyperDbg> prealloc regular-event 10
the requested pools are allocated and reserved

0: kHyperDbg> prealloc epthook 10
the requested pools are allocated and reserved
```

Assume we need to pre-allocate and [reserve a safe buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) for an event in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode). We can use the following command.

```
0: kHyperDbg> prealloc regular-safe-buffer 1
the requested pools are allocated and reserved
```

### &#x20;IOCTL

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

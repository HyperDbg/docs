---
description: Description of '!epthook2' command in HyperDbg.
---

# !epthook2 \(hidden hook with EPT - Detours\)

### Command

> !epthook2

### Syntax

> !epthook2 \[address\] \[event options\]

### Description

In-line, detours-style kernel EPT hidden hook. \(fast\)

{% hint style="info" %}
This implementation of hidden hook won't cause vm-exit when it triggers, it's like detours and everything is done in vmx non-root, so it's much faster than !epthook but it some limitations, see **Remarks** for more information.
{% endhint %}

### Parameters

\[address\]

          The **Virtual** address of where we want to start to disassemble its memory

\[event options\]

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Break to Debugger

### Log the State

### Run Custom Code

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

Use `HIDDEN_HOOK_EXEC_DETOURS` as **EventType**, ****and send the address of where you want to hook in `OptionalParam1`in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### **Remarks**

This command is much faster than **!epthook**, but it has the following limitations:

* It can be used only in kernel addresses, which means that you cannot use it for user-mode addresses.
* You can only use one hook in a page of memory, for example, if you put a hook on `fffff80126551006` then you cannot put another hook in the range of `fffff80126551000` to `fffff80126551fff`  because it's within the same page \(`0x1000` or `4096` bytes\).
* It has the limitation of classic detours hooks, we patch **18** bytes for our detours hook so when you put a hook anywhere in your assembly, you have to make sure that there is no relative jump or relative call within **18** bytes after the hook address. Most of the time, the start address of the function is detours-compatible \(doesn't start with relative jumps or relative calls\), especially in x64 fast call functions thus start address of a function is a good point to put these hidden hooks.
* `r11` is modified in the hook so you need to make sure that the target address doesn't use this register \(at least at that point of the program\). If you need the `r11` compile the HyperDbg with another register \(change the register in `EptHookWriteAbsoluteJump` and `EptHookWriteAbsoluteJump2`in `Ept.c`\).

### Requirements

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

None


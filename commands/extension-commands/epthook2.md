---
description: Description of '!epthook2' command in HyperDbg.
---

# !epthook2 \(hidden hook with EPT - detours\)

### Command

> !epthook2

### Syntax

> !epthook2 \[address\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

In-line, detours-style kernel EPT hidden hook. \(fast\)

{% hint style="info" %}
This implementation of hidden hook won't cause vm-exit when it triggers, it's like detours and everything is done in vmx non-root, so it's much faster than !epthook but it some limitations, see **Remarks** for more information.
{% endhint %}

### Parameters

**\[address\]**

          The **Virtual** address of where we want to put hook

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, we send the **physical** address of where put the hidden hook's breakpoint.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

Imagine we want to put a hook on ``fffff800`4ed6f010``, this will breaks to the debugger when the target address hits and gives the control back to you.

```c
!epthook2 fffff800`4ed6f010 
```

### Log the States

Not Completed Yet !

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information

```c
!epthook2 fffff801deadbeef code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered and also 3 nops condition. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information

```c
!epthook2 fffff801deadbeef code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

Use `HIDDEN_HOOK_EXEC_DETOURS` as **EventType**, ****and send the address of where you want to hook in `OptionalParam1`in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !epthook2](https://docs.hyperdbg.com/design/features/design-of-epthook2)" to see how does it work.

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

[!epthook \(hidden hook with EPT - stealth breakpoints\)](https://docs.hyperdbg.com/commands/extension-commands/epthook)


---
description: Description of '!epthook' command in HyperDbg.
---

# !epthook \(hidden hook with EPT - stealth breakpoints\)

### Command

> !epthook

### Syntax

> !epthook \[address\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Put hidden breakpoint \(0xcc\) on the target function in user-mode and kernel-mode without modifying the content of memory in the case of read/write.

{% hint style="info" %}
This implementation of hidden hook causes vm-exit when it triggers, a faster implementation of EPT hidden hooks is [!epthook2](https://docs.hyperdbg.com/commands/extension-commands/epthook2) which is without vm-exits, but it has some limitations as described in its manual.
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

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the **physical** address of where put the hidden hook's breakpoint.

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
!epthook fffff800`4ed6f010 
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
!epthook fffff801deadbeef code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered and also 3 nops condition. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information

```c
!epthook fffff801deadbeef code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

Use `HIDDEN_HOOK_EXEC_CC` as **EventType**, ****and send the address of where you want to hook in `OptionalParam1`in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !epthook](https://docs.hyperdbg.com/design/features/design-of-epthook)" to see how does it work.

### **Remarks**

This command is much slower than **!epthook2**, ****because it cause vm-exits but on the other hand, this implementation doesn't have any limitation, for example you can use this command for hooking user-mode while you can't use **!epthook2** on user-mode.

### Requirements

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

[!epthook2 \(hidden hook with EPT - detours\)](https://docs.hyperdbg.com/commands/extension-commands/epthook2)


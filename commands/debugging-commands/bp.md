---
description: Description of 'bp' command in HyperDbg.
---

# bp \(set breakpoint\)

### Command

> !bp

### Syntax

> !bp \[address\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Put hidden breakpoint \(0xcc\) on the target function in user-mode and kernel-mode without modifying the content of memory in the case of read/write.

{% hint style="info" %}
In **HyperDbg**, 'bp' command is the same as '[!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook)' command.
{% endhint %}

### Parameters

**\[address\]**

          The **Virtual** address of where we want to put a breakpoint.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the **virtual** address of where put the hidden breakpoint. See the **Remarks** for more information.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

Imagine we want to put a breakpoint on ``fffff800`4ed6f010``, this will breaks to the debugger when the target address hits and gives the control back to you.

```c
bp fffff800`4ed6f010 
```

### Log the States

Not Completed Yet !

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting an idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Putting a breakpoint on `fffff801deadbeef` and run 3 nops whenever the breakpoint is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
bp fffff801deadbeef code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Putting a breakpoint on `fffff801deadbeef` and run 3 nops whenever the breakpoint is triggered and also 3 nops condition. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
bp fffff801deadbeef code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

Use `HIDDEN_HOOK_EXEC_CC` as **EventType**, ****and send the address of where you want to hook in `OptionalParam1`in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !epthook](https://docs.hyperdbg.com/design/features/vmm-module/design-of-epthook)" to see how does it work.

### **Remarks**

You can disable or clear breakpoints using the '[events](https://docs.hyperdbg.com/commands/debugging-commands/events)' command.

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, **bp** and **!epthook2** commands in the same page \(4KB\) simultaneously, for example, when you put a hidden hook \(**!epthook2**\) on **0x10000005** you shouldn't use any of **!monitor** or **!epthook** or **bp** commands on the address starting from **0x10000000** to **0x10000fff**.

 You can use **!epthook** or **bp** \(just _**!epthook**_ not **!epthook2** and not **!monitor**\) on two or more addresses in the same page \(means that you can use the **!epthook** or **bp** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page\). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

### Requirements

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

[!epthook \(hidden hook with EPT - stealth breakpoints\)](https://docs.hyperdbg.com/commands/extension-commands/epthook)

[!epthook2 \(hidden hook with EPT - detours\)](https://docs.hyperdbg.com/commands/extension-commands/epthook2)


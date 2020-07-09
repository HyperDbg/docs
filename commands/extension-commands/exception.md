---
description: Description of '!exception' command in HyperDbg.
---

# !exception \(hook first 32 entries of IDT\)

### Command

> !exception

### Syntax

> !exception \[IDT Index \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Triggers when the debugging machine encounters an exception \(**faults, traps, aborts**\) or NMI or interrupt. This command applies to only the first 32 entries of IDT \(Interrupt Descriptor Table\), if you need to hook entries between 32 to 256 of IDT then you should use [!interrupt](https://docs.hyperdbg.com/commands/extension-commands/interrupt) instead.

{% hint style="info" %}
When you enable this event, only your specific entry will be hooked, so this command won't trigger on all exceptions/interrupts thus won't make your computer slow but on the other hand, by using [!interrupt](https://docs.hyperdbg.com/commands/extension-commands/interrupt) command, if you just need one of the entries then all entries between **32** to **256** should be emulated by **HyperDbg** so it's substantially slower.
{% endhint %}

### Parameters

**\[IDT Index \(hex value\)\]**

          Trigger in the case of receiving an interrupt or exception. The value should be between **0x0** to **0x1f**. ****If you don't specify this parameter then it will be triggered for all first 32 exceptions/interrupts.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends vector or IDT index of the exception or interrupt.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

Imagine we want to break on all first 32 exceptions and interrupts.

```c
!exception
```

If we want to break on **page-faults**.

```c
!exception 0xe
```

If we want to break on **division-by-zero** on core **1** and process id **0x490**.

```c
!exception 0x0 core 1 pid 490
```

### Log the States

Not Completed Yet!

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring occurrence of first 32 exceptions and interrupts ****and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!exception code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring occurrence of first 32 exceptions and interrupts and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!exception code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use `EXCEPTION_OCCURRED` and send the special entry between **0x0** to **0x1f** \(if any\) if you want to monitor just a special exception or interrupt in `OptionalParam1` in  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !exception & !interrupt](https://docs.hyperdbg.com/design/features/design-of-exception-and-interrupt)" to see how does it work.

### **Remarks**

Emulating page-fault \(entry **0xe**\) is treated differently in HyperDbg, take a look at [here ](https://docs.hyperdbg.com/design/features/design-of-exception-and-interrupt)for more information.

### Requirements

None

### Related

[!interrupt \(hook external device interrupts\)](https://docs.hyperdbg.com/commands/extension-commands/interrupt)


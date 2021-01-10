---
description: Description of '!interrupt' command in HyperDbg.
---

# !interrupt \(hook external device interrupts\)

### Command

> !interrupt

### Syntax

> !interrupt \[IDT Index \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]

### Description

Triggers when the debugging machine encounters an external-interrupt. This command applies to only **32** to **255** entries of IDT \(Interrupt Descriptor Table\), if you need to hook entries between **0** to **31** of IDT then you should use [!exception](https://docs.hyperdbg.com/commands/extension-commands/exception) instead.

{% hint style="info" %}
When you enable this event, all entries from **32** to **255** will cause vm-exits, so this command will trigger on all external-interrupts thus make your computer substantially slower. This is not true about [!exception](https://docs.hyperdbg.com/commands/extension-commands/exception) command as it will only trigger on that specific entry.
{% endhint %}

### Parameters

**\[IDT Index \(hex value\)\]**

          Trigger in the case of receiving an external-interrupt. The value should be between **0x20** to **0xff**. ****If you don't specify this parameter then it will be triggered for all external-interrupts.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[imm \(yes\|no\)\]**

          Optional value in which `yes` means the results \(printed texts in scripts\) should be delivered immediately to the debugger. `no` means that the results can be accumulated and to be delivered as a couple of messages when the buffer is full; thus, it's substantially faster but it's not real-time. By default, this value is set to `yes`.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends vector or IDT index of the external-interrupt.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**" and "**Custom Code**".
{% endhint %}

### Break

Imagine we want to break on entry **0x25** of IDT.

```c
HyperDbg> !interrupt 0x25
```

If we want to break on external-interrupt \(entry **0x25\)** from process id **0x490**.

```c
HyperDbg> !interrupt 0x25 pid 490
```

### Script

Using the following command you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples). 

```
HyperDbg> !interrupt 0x25 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !interrupt 0x25 script { HyperDbg Script Here } imm no
```

**Script \(From File\)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`. 

```
HyperDbg> !interrupt 0x25 script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding) if you want to forward the event monitoring results from this event and other events to an external source e.g. **File**, **NamedPipe**, or **TCP Socket**. This way you can use **HyperDbg** as a monitoring tool and gather the behavior of your target system and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring the occurrence of external-interrupts ****and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
HyperDbg> !interrupt code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring the occurrence of external-interrupts and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
HyperDbg> !interrupt code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use `EXTERNAL_INTERRUPT_OCCURRED` and send the special entry between **0x20** to **0xff** \(if any\) if you want to monitor just a special external-interrupt in `OptionalParam1` in  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

Please look at **Remarks** for more information.

### Design

Take a look at "[Design of !exception & !interrupt](https://docs.hyperdbg.com/design/features/vmm-module/design-of-exception-and-interrupt)" to see how does it work.

### **Remarks**

You should avoid monitoring all external-interrupt because it is generally impossible, for example, thousands of clock-interrupts will be received and if you want to handle all of them then it makes your system unresponsive. By the way, you can monitor just one external-interrupt without problem.

### Requirements

None

### Related

[!exception \(hook first 32 entries of IDT\)](https://docs.hyperdbg.com/commands/extension-commands/exception)


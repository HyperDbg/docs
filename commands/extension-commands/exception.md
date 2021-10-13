---
description: Description of '!exception' command in HyperDbg.
---

# !exception (hook first 32 entries of IDT)

### Command

> !exception

### Syntax

> !exception \[IDT Index (hex value)] \[pid (hex value)] \[core (hex value)] \[imm (yes|no)] \[event options]

### Description

Triggers when the debugging machine encounters an exception (**faults, traps, aborts**) or NMI or interrupt. This command applies to only the first 32 entries of IDT (Interrupt Descriptor Table). If you need to hook entries between 32 to 255 of IDT, you should use [!interrupt](https://docs.hyperdbg.org/commands/extension-commands/interrupt) instead.

{% hint style="info" %}
When you enable this event, only your specific entry will be hooked, so this command won't trigger on all exceptions/interrupts; thus, it won't make your computer slow but on the other hand, by using [!interrupt](https://docs.hyperdbg.org/commands/extension-commands/interrupt) command, if you just need one of the entries; still, all entries between **32** to **255** should be emulated by **HyperDbg**, so it's substantially slower.
{% endhint %}

### Parameters

**\[IDT Index (hex value)]**

Trigger in the case of receiving an interrupt or exception. The value should be between **0x0** to **0x1f** (starting from zero). If you don't specify this parameter, it will be triggered for all first 32 exceptions/interrupts.

**\[pid (hex value)]**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

**\[core (hex value)]**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm (yes|no)]**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[event options]**

Regular event parameters that are used in HyperDbg events. (For more information, read [this ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites)topic)

### Context

As the **Context** (`r8` in custom code and `rdx` in condition code register) to the event trigger, **HyperDbg** sends vector or IDT index of the exception or interrupt.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

Imagine we want to break on all first 32 exceptions and interrupts.

```c
HyperDbg> !exception
```

If we want to break on **page-faults**.

```c
HyperDbg> !exception 0xe
```

If we want to break on **division-by-zero** on core **1** and process id **0x490**.

```c
HyperDbg> !exception 0x0 core 1 pid 490
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !exception 0xe script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !exception 0xe script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !exception 0xe script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read "[How to create an action?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about how to run the custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

**Run Custom Code (Unconditional)**

Monitoring occurrence of first 32 exceptions and interrupts and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !exception code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring occurrence of first 32 exceptions and interrupts and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !exception code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/design/debugger-internals/ioctl-requests-for-events).

As **EventType** use `EXCEPTION_OCCURRED` and send the special entry between **0x0** to **0x1f** (if any) if you want to monitor just a special exception or interrupt in `OptionalParam1` in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !exception & !interrupt](https://docs.hyperdbg.org/design/features/vmm-module/design-of-exception-and-interrupt)" to see how it works.

### Remarks

Emulating page-fault (entry **0xe**) is treated differently in HyperDbg. Take a look [here ](https://docs.hyperdbg.org/design/features/design-of-exception-and-interrupt)for more information.

If the debugger breaks due to the triggering **!exception** event, [the instrumentation stepping command](https://docs.hyperdbg.org/commands/debugging-commands/i) won't re-inject the event into the debuggee. In other words, the ['i' command](https://docs.hyperdbg.org/commands/debugging-commands/i) will continue the normal execution as if the debuggee never created such an **EXCEPTION**.

This command will re-inject the event to the debuggee after triggering the event (not before it).

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

None

### Related

[!interrupt (hook external device interrupts)](https://docs.hyperdbg.org/commands/extension-commands/interrupt)

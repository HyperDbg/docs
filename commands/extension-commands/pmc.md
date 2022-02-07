---
description: Description of '!pmc' command in HyperDbg.
---

# !pmc (hook RDPMC instruction execution)

### Command

> !pmc

### Syntax

> !pmc \[pid (hex value)] \[core (hex value)] \[imm (yes|no)] \[event options]

### Description

Triggers when the debugging machine executes **RDPMC** instruction in any level of execution (kernel-mode or user-mode).

{% hint style="danger" %}
Using this command makes **HyperDbg** vulnerable to timing methods to detect the presence of hypervisor. You should not use this command in [transparent-mode](https://docs.hyperdbg.org/tips-and-tricks/considerations/transparent-mode).
{% endhint %}

### Parameters

**\[pid (hex value)]**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this, option then by default, you receive events on all processes.

Still, in the case of user-mode debugging, HyperDbg will apply it only to the current active debugging process (not all the processes). In that case, you can specify `pid all` to intercept events from the entire system.

**\[core (hex value)]**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm (yes|no)]**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[event options]**

Regular event parameters that are used in HyperDbg events. (For more information, read [this ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites)topic)

### Context

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends **NULL**.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

We want to break and get control over all **RDPMC** execution in our system.

```c
HyperDbg> !pmc
```

Imagine we want to break on all **RDPMC** executions of a process id **0x490** in core **2**.

```c
HyperDbg> !pmc pid 490 core 2
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !pmc script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !pmc script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !pmc script {file:c:\users\sina\desktop\script.txt}
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

Monitoring process id **0x490** for **RDPMC** instruction execution and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !pmc pid 490 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring process id **0x490** for **RDPMC** instruction execution and run 3 nops whenever the event condition is triggered and runs 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !pmc pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/design/debugger-internals/ioctl-requests-for-events).

As **EventType** use `PMC_INSTRUCTION_EXECUTION` in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

This command uses **RDPMC** (**EXIT\_REASON\_RDPMC - 15**) vm-exit to implement RDPMC hooks.

### Remarks

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

None

### Related

[!tsc (hook RDTSC/RDTSCP instruction execution)](https://docs.hyperdbg.org/commands/extension-commands/tsc)

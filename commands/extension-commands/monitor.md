---
description: Description of the '!monitor' command in HyperDbg.
---

# !monitor (monitor read/write/execute to a range of memory)

### Command

> !monitor

### Syntax

> !monitor \[Attribute (string)] \[FromAddress (hex)] \[ToAddress (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[stage CallingStage (prepostall)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }] \[output {OutputName (string)}]

### Description

Monitors read or write or execute (or a combination of these operations) to a range of addresses. If any read or write or execute happens on the specified address range (memory), it will be triggered.

{% hint style="info" %}
It is exactly like read/write/execute of Hardware Debug Registers but without any size and count limitation.
{% endhint %}

### Parameters

**\[**Attribute **(string)]**

Can be one of these values (or a combination of these attributes like '**rw**', '**rx**', '**wx**', '**rwx**', etc.):

**r**: trigger in the case of reading.

**w**: trigger in the case of writing.

**x**: trigger in the case of executing.

**\[FromAddress (hex)]**

The start **virtual** address of where it needs to be monitored for reading or writing or executing (or a custom combination of these attributes).

**\[ToAddress (hex)]**

The end of the **virtual** address of where it needs to be monitored for reading or writing or executing (or a custom combination of these attributes).

**\[pid ProcessId (hex)] (optional)**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

Still, in the case of user-mode debugging, HyperDbg will apply it only to the current active debugging process (not all the processes). In that case, you can specify `pid all` to intercept events from the entire system.

**\[core CoreId (hex)] (optional)**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm IsImmediate (yesno)] (optional)**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[sc EnableShortCircuiting (onoff)] (optional)**

Optional value to ignore the emulation (skip execution) of the event. Add `sc on` to your command thus whenever the event is triggered, the effects and the execution of the actual event will be ignored. For more information, please read [this](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting) article. If you don't specify this option, then by default, all the events will be emulated (executed). By default, this value is set to `off`.

**\[stage CallingStage (prepostall)] (optional)**

Optional value to configure the [calling stage](https://docs.hyperdbg.org/tips-and-tricks/misc/event-calling-stage) of the event. To trigger the event **before** the emulation, include `stage pre` in your command. Conversely, using `stage post` will cause the event to be triggered **after** the emulation. Additionally, using `stage all` will trigger the event **both** **before** and **after** the emulation. For more information, please read [this](https://docs.hyperdbg.org/tips-and-tricks/misc/event-calling-stage) article. By default, this value is set to `pre`.

**\[buffer PreAllocatedBuffer (hex)] (optional)**

Optional value which reserves a safe [pre-allocated buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) to be accessed within the event codes.

**\[script { Script (string) }] (optional)**

A HyperDbg [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) will be executed each time the event is triggered.

**\[condition { Condition (hex) }]  (optional)**

Optional hex assembly codes which check for [conditions](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) in assembly.

**\[code { Code (hex) }]  (optional)**

Optional [hex assembly codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) will be executed each time the event is triggered.

**\[output {OutputName (string)}]  (optional)**

Optional output resource name for [forwarding events](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding).

### Context

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the **virtual address** of the memory that has accessed and triggered this event.

It's a virtual address equal to or between **\[from address]** and **\[to address],** so it's not a constant address and might differ in the range you entered.

### Short-circuiting

This event supports **'**[**event short-circuiting**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', which means that you can configure HyperDbg to ignore its execution and its effects. For additional details, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

#### Short-Circuiting Behavior in Read/Write vs. Execution Events

When it comes to short-circuiting events, there are distinct behaviors for reads/writes and executions.

1. Short-circuiting for **Reads**/**Writes**: In this scenario, short-circuiting involves disregarding the execution of commands that read from or write to memory, such as MOV instructions. It is as if these instructions were never executed, and memory modifications are not performed.
2. Short-circuiting for **Execution**: Short-circuiting events for execution operate differently. It involves blocking the execution at the target address. For instance, if you wish to prevent execution on a specific page, you can achieve this by short-circuiting the event. Here is an example:

```clike
!monitor x 7e0000 7e0999 pid 3a4c script {
  event_sc(1);
  printf("target address execution is blocked: %llx\n", $context);
}
```

By adding `event_sc(1);`, HyperDbg is instructed to **block** execution, preventing any code within the target page from running.

If `event_sc(1);` is not used, HyperDbg will allow the target to execute normally for just one instruction before triggering again. The event will then be triggered for the next instruction, and so on. In essence, without `event_sc(1);`, it is like stepping through the instructions one by one, with each instruction in the target address range triggering the event. Conversely, specifying `event_sc(1);` will effectively block execution, preventing the target code on the target page from running.

### Calling Stages

This event supports different [calling stages](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting). For the **Read**/**Write** calling stage, the '**pre**' calling stage is triggered prior to the memory modification, whereas the '**post**' calling stage is triggered subsequent to the memory modification. In addition, the '**all**' calling stage will trigger the event in both cases. Using this mechanism you can see the memory **before and after the modification** (e.g., the **MOV** instruction modification).&#x20;

For the **Execute** calling stage, the '**pre**' calling stage is triggered prior to running the instruction that its execution leads to triggering the event, whereas the '**post**' calling stage is triggered subsequent to running the target instruction. In addition, the '**all**' calling stage will trigger the event in both cases.&#x20;

You can [ignore](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting) the event in the '**pre**' stage. For more information, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

Imagine we want to put a monitor writes but not reads/executes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` , this will break to the debugger and gives the control back to us.

```c
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100
```

If we want reads but not writes/executes.

```c
HyperDbg> !monitor r fffff800`4ed60000 fffff800`4ed60100
```

If we want both reads and writes but not the executes.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100
```

Alternatively, we can use `nt!Kd_DEFAULT_Mask` too.

```c
HyperDbg> !monitor rw nt!Kd_DEFAULT_Mask nt!Kd_DEFAULT_Mask+4
```

If we want to monitor any execution of instructions from this range, we can use the following command.

```c
HyperDbg> !monitor x fffff800`7bd40000 fffff800`7bd40100
```

If we want to monitor any reads, writes, or executions of instructions from a PE section in the user-mode, we can use the following command.

```c
HyperDbg> !monitor rwx 00007ff8`349f2000 00007ff8`349f8000
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script { HyperDbg Script Here }
```

The above command is used when messages don't need to be delivered immediately.

```
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read "[How to create an action?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about how to run a custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in the VMX-root is considered "[unsafe](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

**Run Custom Code (Unconditional)**

Monitoring reads and writes on an address range starting from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring reads and executions on an address range starting from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !monitor rx fffff800`4ed60000 fffff800`4ed60100 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** you can use one of the following events:

```
HIDDEN_HOOK_READ_AND_WRITE_AND_EXECUTE
HIDDEN_HOOK_READ_AND_WRITE
HIDDEN_HOOK_READ_AND_EXECUTE
HIDDEN_HOOK_WRITE_AND_EXECUTE
HIDDEN_HOOK_READ
HIDDEN_HOOK_WRITE
HIDDEN_HOOK_EXECUTE
```

After that, you can send the start address (**from address**) of where you want to monitor in `OptionalParam1`and end address (**to address**) of where you want to monitor in `OptionalParam2`address `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !monitor](https://docs.hyperdbg.org/design/features/vmm-module/design-of-monitor)" to see how it works.

### Remarks

Starting from **v0.4**, the support for **execution** interception or the '`x`' attribute string is added to the HyperDbg debugger.

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, and **!epthook2** commands on the same page (4KB) simultaneously. For example, when you put a hidden hook (**!epthook2**) on **0x10000005**, you shouldn't use any of **!monitor** or **!epthook** commands on the address starting from **0x10000000** to **0x10000fff**.

You can use **!epthook** (just _**!epthook**_ not **!epthook2** and not **!monitor**) on two or more addresses on the same page (means that you can use the **!epthook** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

If you need to reserve more pre-allocated pools for this command, you can use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command.

This command creates an [event](https://docs.hyperdbg.org/design/debugger-internals/events). Starting from HyperDbg **v0.7**, events are guaranteed to keep the debuggee in a halt state (in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)); thus, nothing will change during its execution and the context (registers and memory) remain untouched. You can visit [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) for more information.

### Requirements

Post-Nehalem Processor (EPT)

### Related

[prealloc (reserve pre-allocated pools)](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)

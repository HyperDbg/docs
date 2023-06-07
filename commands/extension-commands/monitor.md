---
description: Description of the '!monitor' command in HyperDbg.
---

# !monitor (monitor read/write to a page)

### Command

> !monitor

### Syntax

> !monitor \[Mode (string)] \[FromAddress (hex)] \[ToAddress (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }]

### Description

Monitors read or write or read/write to a range of addresses. If any read or write on your range address (memory), it will be triggered.

{% hint style="info" %}
It is exactly like read/write of Hardware Debug Registers but without any size and count limitation.
{% endhint %}

### Parameters

**\[Mode (string)]**

Can be one of these values :

**rw** : trigger in the case of reading and writing.

**r** : trigger in the case of reading.

**w** : trigger in the case of writing.

**\[FromAddress (hex)]**

The start **Virtual** address of where we want to monitor for reading or writing.

**\[ToAddress (hex)]**

The end of **Virtual** address of where we want to monitor for reading or writing.

**\[pid ProcessId (hex)] (optional)**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

Still, in the case of user-mode debugging, HyperDbg will apply it only to the current active debugging process (not all the processes). In that case, you can specify `pid all` to intercept events from the entire system.

**\[core CoreId (hex)] (optional)**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm IsImmediate (yesno)] (optional)**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[sc EnableShortCircuiting (onoff)] (optional)**

Optional value to ignore the emulation (skip execution) of the event. Add `sc on` to your command thus whenever the event is triggered, the effects and the execution of the actual event will be ignored. For more information, please read [this](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting) article. If you don't specify this option, then by default, all the events will be emulated (executed). By default, this value is set to `off`.

**\[buffer PreAllocatedBuffer (hex)] (optional)**

Optional value which reserves a safe [pre-allocated buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) to be accessed within the event codes.

**\[script { Script (string) }] (optional)**

A HyperDbg [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) will be executed each time the event is triggered.

**\[condition { Condition (hex) }]  (optional)**

Optional hex assembly codes which check for [conditions](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) in assembly.

**\[code { Code (hex) }]  (optional)**

Optional [hex assembly codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) will be executed each time the event is triggered.

### Context

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the **virtual address** of the memory that has accessed and triggered this event.

It's a virtual address equal to or between **\[from address]** and **\[to address],** so it's not a constant address and might differ in the range you entered.

### Short-circuiting

This event supports **'**[**event short-circuiting**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', which means that you can configure HyperDbg to ignore its execution and its effects. For additional details, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

Imagine we want to put a monitor writes but not reads on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` , this will break to the debugger and gives the control back to you.

```c
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100
```

If we want reads but not writes.

```c
HyperDbg> !monitor r fffff800`4ed60000 fffff800`4ed60100
```

If we want both reads and writes.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100
```

Alternatively, you can use `nt!Kd_DEFAULT_Mask` too.

```c
HyperDbg> !monitor rw nt!Kd_DEFAULT_Mask nt!Kd_DEFAULT_Mask+4
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

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
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

**Run Custom Code (Unconditional)**

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** use `HIDDEN_HOOK_READ` in the case you want just reads, use `HIDDEN_HOOK_WRITE` in the case you want just writes and use `HIDDEN_HOOK_READ_AND_WRITE` in the case you want both reads and writes and send the start address (**from address**) of where you want to monitor in `OptionalParam1`and end address (**to address**) of where you want to monitor in `OptionalParam2`address `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !monitor](https://docs.hyperdbg.org/design/features/vmm-module/design-of-monitor)" to see how does it work.

### Remarks

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, and **!epthook2** commands on the same page (4KB) simultaneously. For example, when you put a hidden hook (**!epthook2**) on **0x10000005**, you shouldn't use any of **!monitor** or **!epthook** commands on the address starting from **0x10000000** to **0x10000fff**.

You can use **!epthook** (just _**!epthook**_ not **!epthook2** and not **!monitor**) on two or more addresses on the same page (means that you can use the **!epthook** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

If you need to reserve more pre-allocated pools for this command, you can use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command.

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

Post-Nehalem Processor (EPT)

### Related

[prealloc (reserve pre-allocated pools)](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)

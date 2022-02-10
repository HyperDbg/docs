---
description: Description of '!epthook2' command in HyperDbg.
---

# !epthook2 (hidden hook with EPT - detours)

### Command

> !epthook2

### Syntax

> !epthook2 \[Address (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }]

### Description

Puts an in-line, detours-style kernel EPT hidden hook (fast).&#x20;

{% hint style="info" %}
This implementation of the hidden hook won't cause vm-exit when it triggers. It's like detours, and everything is done in vmx non-root, so it's much faster than !epthook, but it some limitations. See **Remarks** for more information.
{% endhint %}

### Parameters

**\[Address (hex)]**

The **Virtual** address of where we want to put the hook.

**\[pid ProcessId (hex)] (optional)**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

Still, in the case of user-mode debugging, HyperDbg will apply it only to the current active debugging process (not all the processes). In that case, you can specify `pid all` to intercept events from the entire system.

**\[core CoreId (hex)] (optional)**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm IsImmediate (yesno)] (optional)**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[buffer PreAllocatedBuffer (hex)] (optional)**

Optional value which reserves a safe [pre-allocated buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) to be accessed within the event codes.

**\[script { Script (string) }] (optional)**

A HyperDbg [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) will be executed each time the event is triggered.

**\[condition { Condition (hex) }]  (optional)**

Optional hex assembly codes which check for [conditions](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) in assembly.

**\[code { Code (hex) }]  (optional)**

Optional [hex assembly codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) will be executed each time the event is triggered.

### Context

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the **virtual address** of where you put the hidden hook's breakpoint.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

Imagine we want to put a hook on ``fffff800`4ed6f010``, this will break into the debugger when the target address hits and gives the control back to you.

```c
HyperDbg> !epthook2 fffff800`4ed6f010
```

Alternatively, you can use `nt!ExAllocatePoolWithTag` too.

```c
HyperDbg> !epthook2 nt!ExAllocatePoolWithTag
```

You can also use an expression like `nt!ExAllocatePoolWithTag+@rcx+5` too.

```c
HyperDbg> !epthook2 nt!ExAllocatePoolWithTag+@rcx+5
```

#### Script

{% hint style="info" %}
**Important:** This command operates in vmx non-root mode; however, HyperDbg's script engine is designed to work on vmx root-mode. We prefer to use the script engine in **!epthook2** and won't cause vm-exit to keep this command as fast as possible, thus, you have some limitations on using script engine as is described on "[Script engine in VMX non-root mode](https://docs.hyperdbg.org/tips-and-tricks/considerations/script-engine-in-vmx-non-root-mode)".
{% endhint %}

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !epthook2 fffff800`4ed6f010 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !epthook2 fffff800`4ed6f010 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !epthook2 fffff800`4ed6f010 script {file:c:\users\sina\desktop\script.txt}
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

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !epthook2 fffff801deadbeef code {90 90 90}
```

**Run Custom Code (Conditional)**

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered and also 3 nops condition. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !epthook2 fffff801deadbeef code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/design/debugger-internals/ioctl-requests-for-events).

Use `HIDDEN_HOOK_EXEC_DETOURS` as **EventType**, and send the address of where you want to hook in `OptionalParam1`in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !epthook2](https://docs.hyperdbg.org/design/features/vmm-module/design-of-epthook2)" to see how does it work.

### Remarks

This command is much faster than **!epthook**, but it has the following limitations:

* It can be used only in kernel addresses, which means that you cannot use it for user-mode addresses.
* You can only use one hook in a page of memory. For example, if you put a hook on `fffff80126551006` then you cannot put another hook in the range of `fffff80126551000` to `fffff80126551fff` because it's within the same page (`0x1000` or `4096` bytes).
* It has the limitation of classic detours hooks. We patch **19** bytes for our detours hook, so when you put a hook anywhere in your assembly, you have to make sure that there is no relative jump or relative call within **19** bytes after the hook address. Most of the time, the start address of the function is detours-compatible (doesn't start with relative jumps or relative calls), especially in x64 fast call functions; thus, the start address of a function is a good point to put these hidden hooks.

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, and **!epthook2** commands on the same page (4KB) simultaneously. For example, when you put a hidden hook (**!epthook2**) on **0x10000005**, you shouldn't use any of **!monitor** or **!epthook** commands on the address starting from **0x10000000** to **0x10000fff**.

You can use **!epthook** (just _**!epthook**_ not **!epthook2** and not **!monitor**) on two or more addresses on the same page (means that you can use the **!epthook** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

Post-Nehalem Processor (EPT)

Processor with Execute-only Pages Support

### Related

[!epthook (hidden hook with EPT - stealth breakpoints)](https://docs.hyperdbg.org/commands/extension-commands/epthook)

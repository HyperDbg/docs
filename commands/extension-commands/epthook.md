---
description: Description of '!epthook' command in HyperDbg.
---

# !epthook (hidden hook with EPT - stealth breakpoints)

### Command

> !epthook

### Syntax

> !epthook \[address] \[pid (hex value)] \[core (hex value)] \[imm (yes|no)] \[event options]

### Description

Puts a hidden breakpoint (0xcc) on the target function in user-mode and kernel-mode without modifying the content of memory in the case of reading/writing.

{% hint style="info" %}
This implementation of the hidden hook causes vm-exit when it triggers. A faster implementation of EPT hidden hooks is [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2), which is without vm-exits. Still, it has some limitations, as described in the documentation.
{% endhint %}

{% hint style="danger" %}
If you want to keep the current context without continuing the debuggee, you should use the '[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command instead.
{% endhint %}

### Parameters

**\[address]**

The **Virtual** address of where we want to put the hook.

**\[pid (hex value)]**

Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

**\[core (hex value)]**

Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm (yes|no)]**

Optional value in which `yes` means the results (printed texts in scripts) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to `yes`.

**\[event options]**

Regular event parameters that are used in HyperDbg events. (For more information, read [this ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites)topic)

### Context

As the **Context** (`r8` in custom code and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the **virtual** address of where put the hidden hook's breakpoint, oppose to **!epthook2** all checks are based on **virtual** address, not based on **physical** address. See the **Remarks** for more information.

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
HyperDbg> !epthook fffff800`4ed6f010
```

Alternatively, you can use `nt!ExAllocatePoolWithTag` too.

```c
HyperDbg> !epthook nt!ExAllocatePoolWithTag
```

You can also use an expression like `nt!ExAllocatePoolWithTag+@rcx+5` too.

```c
HyperDbg> !epthook nt!ExAllocatePoolWithTag+@rcx+5
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !epthook fffff800`4ed6f010 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !epthook fffff800`4ed6f010 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !epthook fffff800`4ed6f010 script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read "[How to create an action?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about running a custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

**Run Custom Code (Unconditional)**

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !epthook fffff801deadbeef code {90 90 90}
```

**Run Custom Code (Conditional)**

Putting a hook on `fffff801deadbeef` and run 3 nops whenever the hook is triggered and also 3 nops condition. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !epthook fffff801deadbeef code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/design/debugger-internals/ioctl-requests-for-events).

Use `HIDDEN_HOOK_EXEC_CC` as **EventType**, and send the address of where you want to hook in `OptionalParam1`in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !epthook](https://docs.hyperdbg.org/design/features/vmm-module/design-of-epthook)" to see how does it work.

### Remarks

This command is much slower than **!epthook2**, because it cause vm-exits, but on the other hand, this implementation doesn't have any limitation. For example, you can use this command for hooking user-mode while you can't use **!epthook2** on user-mode.

**Why don't we use a physical address to find this command?**

Generally, it's better to use the physical address. Still, we don't use the physical address here (**!epthook2** uses physical address) because if we want to compare **physical** address, we have to flush TLB (change `Cr3`) to convert **GUEST_RIP** to the physical address. As **HyperDbg** is designed to stick to the **System** process (pid = 4), this cr3 change is unavoidable. On the other hand, this command is designed to work on both user-mode and kernel-mode of random processes, and as you know, flushing TLB makes this command even slower. Hence, it's better to deal with the virtual address.

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, and **!epthook2** commands on the same page (4KB) simultaneously. For example, when you put a hidden hook (**!epthook2**) on **0x10000005**, you shouldn't use any of **!monitor** or **!epthook** commands on the address starting from **0x10000000** to **0x10000fff**.

You can use **!epthook** (just _**!epthook**_ not **!epthook2** and not **!monitor**) on two or more addresses on the same page (means that you can use the **!epthook** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

Post-Nehalem Processor (EPT)

Processor with Execute-only Pages Support

### Related

[bp (set breakpoint)](https://docs.hyperdbg.org/commands/debugging-commands/bp)

[!epthook2 (hidden hook with EPT - detours)](https://docs.hyperdbg.org/commands/extension-commands/epthook2)

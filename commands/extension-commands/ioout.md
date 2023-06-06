---
description: Description of the '!ioout' command in HyperDbg.
---

# !ioout (hook OUT instruction execution)

### Command

> !ioout

### Syntax

> !ioout \[Port (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }]

### Description

Triggers when the debugging machine executes **OUT** or **OUT\*** instructions or, in other words, when Windows or a driver tries to use I/O ports.

{% hint style="info" %}
When you enable this event, only your specific I/O port will be hooked, so this command won't trigger on all I/O ports, thus won't make your computer slow.
{% endhint %}

### Parameters

**\[Port (hex)]**

Trigger in the case of using a special I/O port. If you don't specify this parameter, then it will be triggered for all I/O ports.

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

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the **port** number that the target tries to access.

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

Imagine we want to break on all accesses (**OUT/OUT\*** instructions) to I/O ports.

```c
HyperDbg> !ioout
```

If we want to break on I/O port **0x3f8**.

```c
HyperDbg> !ioout 0x3f8
```

Note that default ports for serial connections are: **0x03f8, 0x02f8, 0x03e8, 0x02e8**.

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !ioout 0x3f8 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !ioout 0x3f8 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !ioout 0x3f8 script {file:c:\users\sina\desktop\script.txt}
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

Monitoring execution of **OUT/OUT\*** instructions for I/O port **0x3f8** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !ioout 0x3f8 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring execution of **OUT/OUT\*** instructions for I/O port **0x3f8** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !ioout 0x3f8 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** use `OUT_INSTRUCTION_EXECUTION` and send the special I/O port (if any) if you want to monitor just an I/O port in `OptionalParam1` in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Both **!ioin** and **!ioout** use the vm-exits caused by setting bits in the I/O Bitmap (I/O Bitmap A, I/O Bitmap B) field of the hypervisor VMCS.

For emulating I/O ports, vm-exit with (**EXIT\_REASON\_IO\_INSTRUCTION**) or exit-reason **30** is used.

### Remarks

You can also modify the content of I/O ports for both **IN** and **OUT** instructions.

This is an event command, but in the current version of HyperDbg (in Debugger Mode), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

None

### Related

[!ioin (hook IN instruction execution)](https://docs.hyperdbg.org/commands/extension-commands/ioin)

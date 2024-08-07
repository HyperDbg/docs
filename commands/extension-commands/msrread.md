---
description: Description of the '!msrread' command in HyperDbg.
---

# !msrread (hook RDMSR instruction execution)

### Command

> !msrread

### Syntax

> !msrread \[Msr (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[stage CallingStage (prepostall)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[asm condition { Condition (assembly/hex) }] \[asm code { Code (assembly/hex) }] \[output {OutputName (string)}]

### Description

Triggers when the debugging machine executes an **RDMSR** instruction or, in other words, when Windows or a driver tries to read a Model-Specific Register (MSR).

### Parameters

**\[Msr (hex)]**

Trigger in the case of a special Model-Specific Register (MSR). If you don't specify this parameter, then it will be triggered for all **RDMSR** executions.

{% hint style="danger" %}
Generally, it's not a good practice to intercept all the MSR Reads (RDMSR) or MSR Writes (WRMSRs) because it makes your system substantially slower and undefined behavior in some cases. By the way, HyperDbg supports intercepting all the MSRs. If you don't specify any parameters to intercept all the MSRs, HyperDbg automatically  `IA32_KERNEL_GSBASE (0xC0000102)`, `IA32_MPERF (0x000000e7)` and `IA32_APERF (0x000000e8)`.&#x20;

If you explicitly specify these MSRs, you'll get the events for these MSRs like other regular MSRs but only use the '**!msrread**' on these MSRs when you know what you want to do.
{% endhint %}

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

**\[asm condition { Condition (assembly/hex) }]  (optional)**

Optional assembly codes which check for [conditions](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) in assembly.

**\[asm code { Code (assembly/hex) }]  (optional)**

Optional [assembly codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) will be executed each time the event is triggered.

**\[output {OutputName (string)}]  (optional)**

Optional output resource name for [forwarding events](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding).

### Context

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the `rcx`register of when **RDMSR** is executed.

### Short-circuiting

This event supports **'**[**event short-circuiting**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', which means that you can configure HyperDbg to ignore its execution and its effects. For additional details, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Calling Stages

This event supports different [calling stages](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting). The '**pre**' calling stage is triggered prior to running the **RDMSR** instruction, whereas the '**post**' calling stage is triggered subsequent to running the **RDMSR** instruction; thus, you can read/modify the **ECX** register or [ignore](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting) the event in the '**pre**' stage, and view/modify the results (**EDX**:**EAX**) in the '**post**' stage. In addition, the '**all**' calling stage will trigger the event in both cases. For more information, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

Imagine we want to break on all **RDMSRs**.

```c
HyperDbg> !msrread
```

If we want to break on MSR **0xc0000082**.

```c
HyperDbg> !msrread 0xc0000082
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !msrread 0xc0000082 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !msrread 0xc0000082 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !msrread 0xc0000082 script {file:c:\users\sina\desktop\script.txt}
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

Monitoring execution of **RDMSR** for MSR **0xc0000082** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !msrread 0xc0000082 code {90 90 90}
```

Or if you want to use assembly codes directly, you can add an `asm` before the `code`.

```clike
HyperDbg> !msrread 0xc0000082 asm code {nop; nop; nop}
```

**Run Custom Code (Conditional)**

Monitoring execution of **RDMSR** for MSR **0xc0000082** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !msrread 0xc0000082 code {90 90 90} condition {90 90 90}
```

Or if you want to use assembly codes directly, you can add an `asm` before the `condition` and also before the `code`.

```clike
HyperDbg> !msrread 0xc0000082 asm code {nop; nop; nop} asm condition {nop; nop; nop}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** use `RDMSR_INSTRUCTION_EXECUTION` and send the special MSR `rcx` (if any) if you want to monitor just a special MSR in `OptionalParam1` in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Both **!msrread** and **!msrwrite** use the vm-exits caused by setting bits in the MSR Bitmap field of the hypervisor VMCS.

For **!msrread** vm-exit with (**EXIT\_REASON\_MSR\_READ**) or exit-reason **31** is used.

For **!msrwrite** vm-exit with (**EXIT\_REASON\_MSR\_WRITE**) or exit-reason **32** is used.

### Remarks

When you enable this event, only your specific MSR will be hooked, so this command won't trigger on all MSRs; thus won't make your computer slow.

This command creates an [event](https://docs.hyperdbg.org/design/debugger-internals/events). Starting from HyperDbg **v0.7**, events are guaranteed to keep the debuggee in a halt state (in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)); thus, nothing will change during its execution and the context (registers and memory) remain untouched. You can visit [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) for more information.

### Requirements

None

### Related

[!msrwrite (hook WRMSR instruction execution)](https://docs.hyperdbg.org/commands/extension-commands/msrwrite)

---
description: Description of the '!mode' command in HyperDbg.
---

# !mode (detect kernel-to-user and user-to-kernel transitions)

### Command

> !mode

### Syntax

> !mode \[Mode (string)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[stage CallingStage (prepostall)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[asm condition { Condition (assembly/hex) }] \[asm code { Code (assembly/hex) }] \[output {OutputName (string)}]

### Description

Triggers when there is a transition from the user-mode to the kernel-mode (e.g., as the result of an interrupt/fault/exception or a system-call) and from kernel-mode to user-mode (e.g., as the result of `IRET` or `SYSRET`) on the **target process**.

{% hint style="danger" %}
Using this event disables the use of other EPT hook commands, including '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' and '[!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2)', as well as the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' commands.
{% endhint %}

{% hint style="info" %}
This event only applies to a selected process; thus, you need to provide a process ID (`pid`).
{% endhint %}

{% hint style="warning" %}
For reducing memory usage, this feature is not automatically initialized in HyperDbg. Before using this command in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), you should use the '[preactivate](https://docs.hyperdbg.org/commands/debugging-commands/preactivate)' command. There is no need for pre-activation in the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode).
{% endhint %}

### Parameters

**\[Mode (string)]**

Can be one of these values (or a combination of these attributes like '**ku**', or '**uk**'):

**u**: trigger in the case of kernel-to-user transition.

**k**: trigger in the case of user-to-kernel transition.&#x20;

**ku** or **uk**: trigger in the case of both kernel-to-user and user-to-kernel transition.

**\[pid ProcessId (hex)]**

Mandatory value to trigger the event in just the specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`.

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

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the context as `3` if the execution is transitioned from the kernel-mode to the user-mode (reached to the **user**) and sends `0` if the execution is transitioned from the user-mode to the kernel-mode (reached to the **kernel**).

### Short-circuiting

This event supports **'**[**event short-circuiting**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', which means that you can configure HyperDbg to ignore its execution and its effects. For additional details, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

Short-circuiting transition from the kernel-mode to the user-mode (`$context=3`) will cause the target user-mode process to go to a halt state (the execution of instructions in the target process in the user-mode is paused). For example, you can use the following script to pause the target process:

```clike
!mode u pid 0x1c0 script {

	//
	// The user-mode execution of the process is paused and
	// instruction fetches in the user-mode is prevented
	//
	event_sc(1);
}
```

On the other hand, using this feature for short-circuiting user-mode to kernel-mode transition (even if it is possible in HyperDbg), but does not make sense in most cases. If you use it without any further consideration and conditions, you will block the execution of system-calls and interrupts and if Windows fails to regain control of the core from the user-mode process (e.g., through the clock interrupt), the entire core and system will come to a halt, so make sure to short-circuit user-to-kernel transition only if you know what you're doing.&#x20;

### Calling Stages

This event does not support [calling stages](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting). Due to the nature of this event, implementing calling stages in this context wouldn't be meaningful.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

We want to break on the **user-mode** execution of a process with the process ID equal to `0x1c0`.

```c
HyperDbg> !mode u pid 0x1c0
```

We want to break on the **kernel-mode** execution of a process with the process ID equal to `0x1c0`.

```c
HyperDbg> !mode k pid 0x1c0
```

We want to break on the **kernel-mode** and the **user-mode** execution of a process with the process ID equal to `0x1c0`.

```c
HyperDbg> !mode ku pid 0x1c0
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```clike
HyperDbg> !mode ku pid 0x1c0 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```clike
HyperDbg> !mode ku pid 0x1c0 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```clike
HyperDbg> !mode ku pid 0x1c0 script {file:c:\users\sina\desktop\script.txt}
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

Monitoring transitions of process id **0x490** from user-mode to kernel-mode and kernel-mode to user-mode and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !mode ku pid 490 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring transitions of process id **0x490** from user-mode to kernel-mode and kernel-mode to user-mode and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !mode ku pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** use `TRAP_EXECUTION_MODE_CHANGED` and set `OptionalParam1`  to one of the following values based on your need (user-mode transition or kernel-mode transition or both of them):

```
DEBUGGER_EVENT_MODE_TYPE_USER_MODE_AND_KERNEL_MODE = 1
DEBUGGER_EVENT_MODE_TYPE_USER_MODE                 = 3
DEBUGGER_EVENT_MODE_TYPE_KERNEL_MODE               = 0
```

### Design

This command uses the **Mode-Based Execution Control (MBEC)** feature of Intel processors by allocating and adjusting two separate EPT tables (one **Kernel** execution is **disabled** and one **User** execution is **disabled**) and once the target process is identified (using MOV-to-CR3 exiting VM-exits), exchanges the newly allocated EPT page-tables and trigger events once EPT violation happened in the target process.

### Remarks

This command creates an [event](https://docs.hyperdbg.org/design/debugger-internals/events). Starting from HyperDbg **v0.7**, events are guaranteed to keep the debuggee in a halt state (in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)); thus, nothing will change during its execution and the context (registers and memory) remain untouched. You can visit [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) for more information. Please note that in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), this event is not initialized by default, you need to use the '[preactivate](https://docs.hyperdbg.org/commands/debugging-commands/preactivate)' command (only one time) to initial this event. The '[preactivate](https://docs.hyperdbg.org/commands/debugging-commands/preactivate)' command will continue the debuggee but this event won't continue the debuggee, so you need to make sure to execute the '[preactivate](https://docs.hyperdbg.org/commands/debugging-commands/preactivate)' command before using it to avoid losing the context.

The support for this command is added starting from **v0.8**.

This command cannot be used simultaneously with regular EPT hook commands ([!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook), [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2), and [!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)) and the '[.start](https://docs.hyperdbg.org/commands/meta-commands/.start)', or the '[.restart](https://docs.hyperdbg.org/commands/meta-commands/.restart)' commands.

### Requirements

This command requires [**Kaby Lake**](https://en.wikipedia.org/wiki/Kaby\_Lake) (7th generation) or a newer Intel processor that supports **Mode-Based Execution Control** (a.k.a., **MBEC**).

### Related

[preactivate (pre-activate special functionalities)](https://docs.hyperdbg.org/commands/debugging-commands/preactivate)

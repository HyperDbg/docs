---
description: Description of '!syscall, !syscall2' commands in HyperDbg.
---

# !syscall, !syscall2 (hook system-calls)

### Command

> !syscall
>
> !syscall2

### Syntax

> !syscall \[SyscallNumber (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[stage CallingStage (prepostall)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }] \[output {OutputName (string)}]
>
> !syscall2 \[SyscallNumber (hex)] \[pid ProcessId (hex)] \[core CoreId (hex)] \[imm IsImmediate (yesno)] \[sc EnableShortCircuiting (onoff)] \[stage CallingStage (prepostall)] \[buffer PreAllocatedBuffer (hex)] \[script { Script (string) }] \[condition { Condition (hex) }] \[code { Code (hex) }] \[output {OutputName (string)}]

### Description

Triggers when the debugging machine executes a **syscall** instruction or, in other words, when Windows tries to run a system call, this event will be triggered.

{% hint style="danger" %}
If this command produces weird behavior in your system, you can use the [alternative](https://docs.hyperdbg.org/commands/extension-commands/syscall#alternative-method-for-syscall-interception) method.
{% endhint %}

{% hint style="info" %}
When you enable this event, all **syscall** instructions from all processes will be monitored, and due to the limitation in hardware, you can't expect it to trigger for just one process. Still, you can configure the debugger to trigger the event for you in the case of a special process by adding `pid xx`to the command.
{% endhint %}

### Parameters

**\[SyscallNumber (hex)]**

Trigger in the case of a special system-call (system-call number). If you don't specify this parameter, then it will be triggered for all system-calls.

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

As the **Context** (`$context` pseudo-register in the event's script, `r8` in custom code, and `rdx` in condition code register) to the event trigger, **HyperDbg** sends the system-call number, which is `rax` register in Windows system-call calling convention.

### Short-circuiting

This event supports **'**[**event short-circuiting**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', which means that you can configure HyperDbg to ignore its execution and its effects. For additional details, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Calling Stages

This event supports different [calling stages](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting). The '**pre**' calling stage is triggered prior to the kernel transfer, whereas the '**post**' calling stage is triggered subsequent to the kernel transfer. In addition, the '**all**' calling stage will trigger the event in both cases. You can [ignore](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting) the event in the '**pre**' stage. For more information, please refer to the article provided [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting).

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

#### Break

You can use the [condition ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)if you want to check the syscall parameters. For example, Windows uses an x64 fast-call calling convention on its system-call entry; thus, you can check whether parameters of **syscall** `rcx,rdx,r8, etc.` match to your debugging logic or not and have a conditional syscall hooker, and also, you can change the parameters of a **syscall**.

Imagine we want to break on all system-calls of a process id **0x490**.

```c
HyperDbg> !syscall pid 490
```

Suppose we want to break on system-call **0x55** in process id **0x490**. Take a look at the [Syscall Tables](https://j00ru.vexillium.org/syscalls/nt/64/).

```c
HyperDbg> !syscall 55 pid 490
```

#### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces (`HyperDbg Script Here`) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```
HyperDbg> !syscall 55 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !syscall 55 script { HyperDbg Script Here } imm no
```

**Script (From File)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```
HyperDbg> !syscall 55 script {file:c:\users\sina\desktop\script.txt}
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

Monitoring process id **0x490** for syscall-number **0x55** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !syscall 55 pid 490 code {90 90 90}
```

**Run Custom Code (Conditional)**

Monitoring process id **0x490** for syscall-number **0x55** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !syscall 55 pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/using-hyperdbg/sdk/ioctl/event-registration).

As **EventType** use `SYSCALL_HOOK_EFER_SYSCALL`and send the start syscall-number (if any) if you want just a special system-call in `OptionalParam1` in `DEBUGGER_GENERAL_EVENT_DETAIL`.

### Design

Take a look at "[Design of !syscall & !sysret](https://docs.hyperdbg.org/design/features/vmm-module/design-of-syscall-and-sysret)" to see how it works.

### Remarks

{% hint style="danger" %}
This command is not PatchGurad compatible, which means that PatchGuard detects this command and will cause BSOD; thus, make sure to turn it off (e.g., attaching a kernel-mode WinDbg debugger at the start of the Windows) before using this command. Disabling Driver Signature Enforcement alone won't turn off the PatchGuard.
{% endhint %}

This command makes your computer substantially slower.

The difference between **!syscall** and **!syscall2** is that we safely check the memory in the first command to see if the instruction that caused **#UD** is really an **SYSRET** or a **SYSCALL**. So, we access the memory in this command. However, we realized that older systems have problems with this way of memory access. In the second command, we just check for the RIP to see if it's a kernel address or a user address. Usually, this method works without error for several hours, but if one application generates a **#UD**, then a BSOD will happen. The second method is generally faster in speed, but we encourage you to use the first command and if your computer doesn't support the first command, then use the second command.

This command creates an [event](https://docs.hyperdbg.org/design/debugger-internals/events). Starting from HyperDbg **v0.7**, events are guaranteed to keep the debuggee in a halt state (in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)); thus, nothing will change during its execution and the context (registers and memory) remain untouched. You can visit [instant events](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) for more information.

#### Alternative Method For SYSCALL Interception

If the **!syscall** command triggered some weird behavior in your system, you could alternatively use the EPT hook on the SYSCALL handler. For example, you can use the following approach (script).

```clike
!epthook nt!KiSystemCall64+3 script {
    //
    // Your target script here
    //
}
```

The above code sets a breakpoint on the Windows System-call handler and `+3` is added because normally the first instruction in the SYSCALL handler is `SWAPGS`, which we want to be changed because the interception is done in the kernel.

In case you don't have access to Windows symbols, you can read the address of `nt!KiSystemCall64` from MSR `0xc0000082` using the '[rdmsr](https://docs.hyperdbg.org/commands/debugging-commands/rdmsr)' command. After that, add `+3` to the target MSR and run the above code without the function name.

### Requirements

None

### Related

[Windows X86-64 System Call Table (XP/2003/Vista/2008/7/2012/8/10)](https://j00ru.vexillium.org/syscalls/nt/64/)

[!sysret (hook SYSRET instruction execution)](https://docs.hyperdbg.org/commands/extension-commands/sysret)

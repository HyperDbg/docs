---
description: Description of '!sysret' command in HyperDbg.
---

# !sysret, !sysret2 \(hook SYSRET instruction execution\)

## Command

> !sysret
>
> !sysret2

## Syntax

> !sysret \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]
>
> !sysret2 \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]

## Description

Triggers when the debugging machine executes a **sysret** instruction or, in other words, when Windows tries to return to user-mode from a previous **syscall**.

{% hint style="info" %}
When you enable this event, all **sysret** instructions from all processes will be monitored, and due to the limitation in hardware, you can't expect it to trigger for just one process. Still, you can configure the debugger to trigger the event for you in the case of a special process by adding `pid xx`to the command.
{% endhint %}

{% hint style="success" %}
The difference between **!sysret** and **!sysret2** is that we safely check the memory in the first command to see if the instruction that caused **\#UD** is really an **SYSRET** or a **SYSCALL**. So, we access the memory in this command. However, we realized that older systems have problems with this way of memory access. In the second command, we just check for the RIP to see if it's a kernel address or a user address. Usually, this method works without error for several hours, but if one application generates a **\#UD**, then a BSOD will happen. The second method is generally faster in speed, but we encourage you to use the first command and if your computer doesn't support the first command, then use the second command.
{% endhint %}

## Parameters

**\[pid \(hex value\)\]**

```text
      Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.
```

**\[core \(hex value\)\]**

```text
      Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.
```

**\[imm \(yes\|no\)\]**

```text
      Optional value in which `yes` means the results \(printed texts in scripts\) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to  `yes`.
```

**\[event options\]**

```text
      Regular event parameters are used in HyperDbg events. \(For more information, read [this ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites)topic\)
```

## Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the `rip` register of where executes the **sysret** instruction. Generally, it should be the same in value in Windows \(just one **sysret** instruction is in Windows\).

## Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read "[How to create a condition?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

## Break

You can use [condition ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)if you want to check the sysret parameters, for example, you can check the result of a previous **syscall** which now wants to return to user-mode by executing **sysret** instruction to see if it matches to your debugging logic or not and have a conditional sysret hooker and also you can change the parameters of a **sysret**.

Imagine we want to break on all **sysret** executions of a process id **0x490**.

```c
HyperDbg> !sysret pid 490
```

## Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.org/commands/scripting-language/examples).

```text
HyperDbg> !sysret script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```text
HyperDbg> !sysret script { HyperDbg Script Here } imm no
```

**Script \(From File\)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`.

```text
HyperDbg> !sysret script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

## Custom Code

Please read "[How to create an action?](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about how to run the custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

### Run Custom Code \(Unconditional\)

Monitoring process id **0x490** for **sysret** instruction execution _\*\*_and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !sysret pid 490 code {90 90 90}
```

### Run Custom Code \(Conditional\)

Monitoring process ID **0x490** for **sysret** instruction execution and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !sysret pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

## IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.org/design/debugger-internals/ioctl-requests-for-events).

As **EventType** use `SYSCALL_HOOK_EFER_SYSRET` in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

## Design

Take a look at "[Design of !syscall & !sysret](https://docs.hyperdbg.org/design/features/vmm-module/design-of-syscall-and-sysret)" to see how it works.

## **Remarks**

{% hint style="danger" %}
This command is not PatchGurad compatible, which means that PatchGuard detects this command and will cause BSOD; thus, make sure to turn it off \(by disabling driver signature enforcement or attaching a Windbg debugger to the Windows\) before using this command.
{% endhint %}

This command makes your computer substantially slower.

This is an event command, but in the current version of HyperDbg \(in Debugger Mode\), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

## Requirements

Post-Nehalem Processor \(EPT\)

## Related

[Windows X86-64 System Call Table \(XP/2003/Vista/2008/7/2012/8/10\)](https://j00ru.vexillium.org/syscalls/nt/64/)

[!syscall \(hook system-calls\)](https://docs.hyperdbg.org/commands/extension-commands/syscall)


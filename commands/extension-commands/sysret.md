---
description: Description of '!sysret' command in HyperDbg.
---

# !sysret \(hook SYSRET instruction execution\)

### Command

> !sysret

### Syntax

> !sysret \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]

### Description

Triggers when the debugging machine executes a **sysret** instruction or in the other words when Windows tries to return to user-mode from a previous **syscall**.

{% hint style="info" %}
When you enable this event, all **sysret** instructions from all processes will be monitored and due to the limitation in hardware you can't expect it to trigger for just one process, but you can configure the debugger to trigger the event for you in the case of a special process by adding `pid xx`to the command.
{% endhint %}

### Parameters

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[imm \(yes\|no\)\]**

          Optional value in which `yes` means the results \(printed texts in scripts\) should be delivered immediately to the debugger. `no` means that the results can be accumulated and to be delivered as a couple of messages when the buffer is full; thus, it's substantially faster but it's not real-time. By default, this value is set to  `yes`.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the `rip` register of where executes the **sysret** instruction, generally, it should be the same in value in Windows \(just one **sysret** instruction is in Windows\).

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**" and "**Custom Code**".
{% endhint %}

### Break

You can use [condition ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)if you want to check the sysret parameters, for example, you can check the result of a previous **syscall** which now wants to return to user-mode by executing **sysret** instruction to see if it matches to your debugging logic or not and have a conditional sysret hooker and also you can change the parameters of a **sysret**.

Imagine we want to break on all **sysret** executions of a process id **0x490**.

```c
!sysret pid 490 
```

### Script

Using the following command you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples). 

```
!sysret script { HyperDbg Script Here }
```

**Script \(From File\)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`. 

```
!sysret script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding) if you want to forward the event monitoring results from this event and other events to an external source e.g. **File**, **NamedPipe**, or **TCP Socket**. This way you can use **HyperDbg** as a monitoring tool and gather the behavior of your target system and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring process id **0x490** for **sysret** instruction execution ****and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!sysret pid 490 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring process id **0x490** for **sysret** instruction execution and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!sysret pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use `SYSCALL_HOOK_EFER_SYSRET` in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !syscall & !sysret](https://docs.hyperdbg.com/design/features/vmm-module/design-of-syscall-and-sysret)" to see how does it work.

### **Remarks**

{% hint style="danger" %}
This command is not PatchGurad compatible, which means that PatchGuard detects this command and will cause BSOD, thus make sure to turn it off \(by disabling driver signature enforcement or attaching a Windbg debugger to the Windows\) before using this command.
{% endhint %}

This command makes your computer slower.

### Requirements

Post-Nehalem Processor \(EPT\)

### Related

[Windows X86-64 System Call Table \(XP/2003/Vista/2008/7/2012/8/10\)](https://j00ru.vexillium.org/syscalls/nt/64/)

[!syscall \(hook system-calls\)](https://docs.hyperdbg.com/commands/extension-commands/syscall)


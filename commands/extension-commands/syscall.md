---
description: Description of '!syscall' command in HyperDbg.
---

# !syscall \(hook system-calls\)

### Command

> !syscall

### Syntax

> !syscall \[syscall-number \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Triggers when the debugging machine executes a **syscall** instruction or in the other words when Windows tries to run a system-call this event will be triggered.

{% hint style="info" %}
When you enable this event, all **syscall** instructions from all processes will be monitored and due to the limitation in hardware you can't expect it to trigger for just one process, but you can configure the debugger to trigger the event for you in the case of a special process by adding `pid xx`to the command.
{% endhint %}

### Parameters

**\[syscall-number \(hex value\)\]**

          Trigger in the case of a special system-call \(system-call number\). If you don't specify this parameter then it will be triggered for all system-calls.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the system-call number which is `rax` register in Windows system-call calling convention.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

You can use [condition ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)if you want to check the syscall parameters, for example, Windows uses an x64 fast-call calling convention on its system-call entry thus you can check whether parameters of **syscall** `rcx,rdx,r8, etc.` match to your debugging logic or not and have a conditional syscall hooker and also you can change the parameters of a **syscall**.

Imagine we want to break on all system-calls of a process id **0x490**.

```c
!syscall pid 490 
```

If we want to break on system-call **0x55** in process id **0x490**. Take a look at the [Syscall Tables](https://j00ru.vexillium.org/syscalls/nt/64/).

```c
!syscall 55 pid 490  
```

### Log the States

Not Completed Yet!

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring process id **0x490** for syscall-number **0x55** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information

```c
!syscall 55 pid 490 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring process id **0x490** for syscall-number **0x55** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information

```c
!syscall 55 pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use  `SYSCALL_HOOK_EFER_SYSCALL`and send the start syscall-number \(if any\) if you want just a special system-call in `OptionalParam1` in  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !syscall & !sysret](https://docs.hyperdbg.com/design/features/design-of-syscall-and-sysret)" to see how does it work.

### **Remarks**

This command is not PatchGurad compatible, which means that PatchGuard detects this command and will cause BSOD, thus make sure to turn it off \(by disabling driver signature enforcement or attaching a Windbg debugger to the Windows\) before using this command.

This command makes your computer slower.

### Requirements

Post-Nehalem Processor \(EPT\)

### Related

[Windows X86-64 System Call Table \(XP/2003/Vista/2008/7/2012/8/10\)](https://j00ru.vexillium.org/syscalls/nt/64/)

[!sysret \(hook SYSRET instruction execution\)](https://docs.hyperdbg.com/commands/extension-commands/sysret)


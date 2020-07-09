---
description: Description of '!pmc' command in HyperDbg.
---

# !pmc \(hook RDPMC instruction execution\)

### Command

> !pmc

### Syntax

> !pmc \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Triggers when the debugging machine executes **RDPMC** instruction in any level of execution \(kernel-mode or user-mode\).

{% hint style="danger" %}
Using this command makes **HyperDbg** vulnerable to timing methods to detect the presence of hypervisor, you should not use this command in [transparent-mode](https://docs.hyperdbg.com/tips-and-tricks/considerations/transparent-mode).
{% endhint %}

### Parameters

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends **NULL**.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

We want to break and get control over all **RDPMC** execution in our system.

```c
!pmc
```

Imagine we want to break on all **RDPMC** executions of a process id **0x490** in core **2**.

```c
!pmc pid 490 core 2
```

### Log the States

Not Completed Yet!

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring process id **0x490** for **RDPMC** instruction execution ****and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!pmc pid 490 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring process id **0x490** for **RDPMC** instruction execution and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!pmc pid 490 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use `PMC_INSTRUCTION_EXECUTION` in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

This command uses **RDPMC** \(**EXIT\_REASON\_RDPMC- 15**\) vm-exit to implement RDPMC hooks.

### **Remarks**

None

### Requirements

None

### Related

[!tsc \(hook RDTSC/RDTSCP instruction execution\)](https://docs.hyperdbg.com/commands/extension-commands/tsc)


---
description: Description of '!msrwrite' command in HyperDbg.
---

# !msrwrite \(hook WRMSR instruction execution\)

### Command

> !msrwrite

### Syntax

> !msrwrite \[msr \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Triggers when the debugging machine executes an **WRMSR** instruction or in the other words when Windows or a driver tries to write on a Model-Specific Register \(MSR\).

{% hint style="info" %}
When you enable this event, only your specific MSR will be hooked, so this command won't trigger on all MSRs thus won't make your computer slow.
{% endhint %}

### Parameters

**\[msr \(hex value\)\]**

          Trigger in the case of a special Model-Specific Register \(MSR\). If you don't specify this parameter then it will be triggered for all **WRMSR** executions.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the `rcx`register of when **WRMSR** executed.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**" and "**Custom Code**".
{% endhint %}

### Break

Imagine we want to break on all **WRMSRs**.

```c
!msrwrite
```

If we want to break on MSR **0xc0000082**.

```c
!msrwrite 0xc0000082  
```

### Script

Not Completed Yet!

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring execution of **WRMSR** for MSR **0xc0000082** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!msrwrite 0xc0000082 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring execution of **WRMSR** for MSR **0xc0000082** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!msrwrite 0xc0000082 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use  `WRMSR_INSTRUCTION_EXECUTION` and send the special MSR `rcx` \(if any\) if you want to monitor just a special MSR in `OptionalParam1` in  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Both **!msrread** and **!msrwrite** use the vm-exits caused by setting bits in the MSR Bitmap field of the hypervisor VMCS.

For **!msrread** vm-exit with \(**EXIT\_REASON\_MSR\_READ**\) or exit-reason **31** is used. 

For **!msrwrite** vm-exit with \(**EXIT\_REASON\_MSR\_WRITE**\) or exit-reason **32** is used. 

### **Remarks**

None

### Requirements

None

### Related

[!msrread \(hook RDMSR instruction execution\)](https://docs.hyperdbg.com/commands/extension-commands/msrread)


---
description: Description of '!msrwrite' command in HyperDbg.
---

# !msrwrite \(hook WRMSR instruction execution\)

### Command

> !msrwrite

### Syntax

> !msrwrite \[msr \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]

### Description

Triggers when the debugging machine executes a **WRMSR** instruction or, in other words, when Windows or a driver tries to write on a Model-Specific Register \(MSR\).

{% hint style="info" %}
When you enable this event, only your specific MSR will be hooked, so this command won't trigger on all MSRs thus won't make your computer slow.
{% endhint %}

### Parameters

**\[msr \(hex value\)\]**

          Trigger in the case of a special Model-Specific Register \(MSR\). If you don't specify this parameter, then it will be triggered for all **WRMSR** executions.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm \(yes\|no\)\]**

          Optional value in which `yes` means the results \(printed texts in scripts\) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to  `yes`.

**\[event options\]**

          Regular event parameters that are used in HyperDbg events. \(For more information, read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the `rcx`register of when **WRMSR** is executed.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

### Break

Imagine we want to break on all **WRMSRs**.

```c
HyperDbg> !msrwrite
```

If we want to break on MSR **0xc0000082**.

```c
HyperDbg> !msrwrite 0xc0000082
```

### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples). 

```
HyperDbg> !msrwrite 0xc0000082 script { HyperDbg Script Here }
```

The above command when messages don't need to be delivered immediately.

```
HyperDbg> !msrwrite 0xc0000082 script { HyperDbg Script Here } imm no
```

**Script \(From File\)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`. 

```
HyperDbg> !msrwrite 0xc0000082 script {file:c:\users\sina\desktop\script.txt}
```

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about how to run the custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring execution of **WRMSR** for MSR **0xc0000082** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !msrwrite 0xc0000082 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring execution of **WRMSR** for MSR **0xc0000082** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !msrwrite 0xc0000082 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
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


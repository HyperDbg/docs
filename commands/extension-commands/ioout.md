---
description: Description of '!ioout' command in HyperDbg.
---

# !ioout \(hook OUT instruction execution\)

### Command

> !ioout

### Syntax

> !ioout \[port \(hex value\)\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Triggers when the debugging machine executes **OUT** or **OUT\*** instructions or in the other words when Windows or a driver tries to use I/O ports.

{% hint style="info" %}
When you enable this event, only your specific I/O port will be hooked, so this command won't trigger on all I/O ports thus won't make your computer slow.
{% endhint %}

### Parameters

**\[port \(hex value\)\]**

          Trigger in the case of using a special I/O port. If you don't specify this parameter then it will be triggered for all ****I/O ports.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if the process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the **port** number that the target tries to access.

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**" and "**Custom Code**".
{% endhint %}

### Break

Imagine we want to break on all accesses \(**OUT/OUT\*** instructions\) to I/O ports.

```c
!ioout
```

If we want to break on I/O port **0x3f8**.

```c
!ioout 0x3f8
```

Note that default ports for serial connections are: **0x03f8, 0x02f8, 0x03e8, 0x02e8**.

### Script

Using the following command you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples). 

```
!ioout 0x3f8 script { HyperDbg Script Here }
```

**Script \(From File\)**

If you saved your script into a file then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`. 

```
!ioout 0x3f8 script {file:c:\users\sina\desktop\script.txt}
```

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring execution of **OUT/OUT\*** instructions for I/O port **0x3f8** and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!ioout 0x3f8 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring execution of **OUT/OUT\*** instructions for I/O port **0x3f8** and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!ioout 0x3f8 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use `OUT_INSTRUCTION_EXECUTION` and send the special I/O port \(if any\) if you want to monitor just an I/O port in `OptionalParam1` in  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Both **!ioin** and **!ioout** use the vm-exits caused by setting bits in the I/O Bitmap \(I/O Bitmap A, I/O Bitmap B\) field of the hypervisor VMCS.

For emulating I/O ports, vm-exit with \(**EXIT\_REASON\_IO\_INSTRUCTION**\) or exit-reason **30** is used.  

### **Remarks**

You can also modify the content of I/O ports for both **IN** and **OUT** instructions.

### Requirements

None

### Related

[!ioin \(hook IN instruction execution\)](https://docs.hyperdbg.com/commands/extension-commands/ioin)


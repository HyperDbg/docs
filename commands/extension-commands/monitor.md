---
description: Description of '!monitor' command in HyperDbg.
---

# !monitor \(monitor read/write to a page\)

### Command

> !monitor

### Syntax

> !monitor \[mode\] \[from address\] \[to address\] \[pid \(hex value\)\] \[core \(hex value\)\] \[event options\]

### Description

Monitor read or write or read/write to a range of addresses, in the case of any read or write on your range address \(memory\), it will be triggered.

{% hint style="info" %}
It is exactly like read/write of Hardware Debug Registers but without any limitation in size and count.
{% endhint %}

### Parameters

**\[mode\]**

          Can be one of these values :

          **rw** : trigger in the case of read and write.

          **r** : trigger in the case of read.

          **w** : trigger in the case of write.

**\[from address\]**

          The start  **Virtual** address of where we want to monitor for read or write.

**\[to address\]**

          The end of **Virtual** address of where we want to monitor for read or write.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command thus command will be executed if process id is equal to `xx`. If you don't specify this option then by default you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option then by default you receive events on all cores.

**\[event options\]**

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the **physical** address of where triggered this event. 

It's a physical address representation of what you entered as **\[from address\]** and **\[to address\]** so it's not a constant address and might be different in the range you entered \(in a physical address format\). 

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

Imagine we want to put a monitor writes but not reads on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` , this will break to the debugger and gives the control back to you.

```c
!monitor w fffff800`4ed60000 fffff800`4ed60100 
```

If we want reads but not writes.

```c
!monitor r fffff800`4ed60000 fffff800`4ed60100 
```

If we want both reads and writes.

```c
!monitor rw fffff800`4ed60000 fffff800`4ed60100 
```

### Run Script

Not Completed Yet!

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` ,  and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes), for more information.

```c
!monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` ,  and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition), for more information.

```c
!monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Breaking to Debugger** and **Running script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use  `HIDDEN_HOOK_READ` in the case you want just reads, use `HIDDEN_HOOK_WRITE` in the case you want just writes and use `HIDDEN_HOOK_READ_AND_WRITE` in the case, you want both reads and writes and send the start address \(**from address**\) of where you want to monitor in `OptionalParam1`and end address \(**to address**\) of where you want to monitor in `OptionalParam2`address  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !monitor](https://docs.hyperdbg.com/design/features/vmm-module/design-of-monitor)" to see how does it work.

### **Remarks**

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, **bp** and **!epthook2** commands in the same page \(4KB\) simultaneously, for example, when you put a hidden hook \(**!epthook2**\) on **0x10000005** you shouldn't use any of **!monitor** or **!epthook** or **bp** commands on the address starting from **0x10000000** to **0x10000fff**.

 You can use **!epthook** or **bp** \(just _**!epthook**_ not **!epthook2** and not **!monitor**\) on two or more addresses in the same page \(means that you can use the **!epthook** or **bp** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page\). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

### Requirements

Post-Nehalem Processor \(EPT\)

### Related

None


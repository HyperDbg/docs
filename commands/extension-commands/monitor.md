---
description: Description of '!monitor' command in HyperDbg.
---

# !monitor \(monitor read/write to a page\)

### Command

> !monitor

### Syntax

> !monitor \[mode\] \[from address\] \[to address\] \[pid \(hex value\)\] \[core \(hex value\)\] \[imm \(yes\|no\)\] \[event options\]

### Description

Monitors read or write or read/write to a range of addresses. If any read or write on your range address \(memory\), it will be triggered.

{% hint style="info" %}
It is exactly like read/write of Hardware Debug Registers but without any size and count limitation.
{% endhint %}

### Parameters

**\[mode\]**

          Can be one of these values :

          **rw** : trigger in the case of reading and writing.

          **r** : trigger in the case of reading.

          **w** : trigger in the case of writing.

**\[from address\]**

          The start  **Virtual** address of where we want to monitor for reading or writing.

**\[to address\]**

          The end of **Virtual** address of where we want to monitor for reading or writing.

**\[pid \(hex value\)\]**

          Optional value to trigger the event in just a specific process. Add `pid xx` to your command; thus, the command will be executed if the process id is equal to `xx`. If you don't specify this option, then by default, you receive events on all processes.

**\[core \(hex value\)\]**

          Optional value to trigger the event in just a specific core. Add `core xx` to your command thus command will be executed if core id is equal to `xx`. If you don't specify this option, then by default, you receive events on all cores.

**\[imm \(yes\|no\)\]**

          Optional value in which `yes` means the results \(printed texts in scripts\) should be delivered immediately to the debugger. `no` means that the results can be accumulated and delivered as a couple of messages when the buffer is full; thus, it's substantially faster, but it's not real-time. By default, this value is set to  `yes`.

**\[event options\]**

          Regular event parameters that are used in HyperDbg events. \(For more information, read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, **HyperDbg** sends the **physical** address of where triggered this event. 

It's a physical address representation of what you entered as **\[from address\]** and **\[to address\],** so it's not a constant address and might be different in the range you entered \(in a physical address format\). 

### Debugger

This event supports three debugging mechanisms.

* Break
* Script
* Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break**", "**Script**", and "**Custom Code**".
{% endhint %}

### Break

Imagine we want to put a monitor writes but not reads on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100`` , this will break to the debugger and gives the control back to you.

```c
HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 
```

If we want reads but not writes.

```c
HyperDbg> !monitor r fffff800`4ed60000 fffff800`4ed60100 
```

If we want both reads and writes.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 
```

### Script

Using the following command, you can use HyperDbg's Script Engine. You should replace the string between braces \(`HyperDbg Script Here`\) with your script. You can find script examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples). 

    HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script { HyperDbg Script Here }

The above command when messages don't need to be delivered immediately.

    HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script { HyperDbg Script Here } imm no

**Script \(From File\)**

If you saved your script into a file, then you can add `file:` instead of a script and append the file path to it. For example, the following examples show how you can run a script from `file:c:\users\sina\desktop\script.txt`. 

    HyperDbg> !monitor w fffff800`4ed60000 fffff800`4ed60100 script {file:c:\users\sina\desktop\script.txt}

{% hint style="success" %}
You can use [**event forwarding**](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding) to forward the event monitoring results from this event and other events to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**. This way, you can use **HyperDbg** as a monitoring tool and gather your target system's behavior and use it later or analyze it on other systems.
{% endhint %}

### Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" to get an idea about how to run a custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx-root mode. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100``  and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) for more information.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90}
```

#### Run Custom Code \(Conditional\)

Monitoring reads and writes on address from ``fffff800`4ed60000`` to ``fffff800`4ed60100``  and run 3 nops whenever the event condition is triggered and run 3 nops whenever the event is triggered. Take a look at [Run Custom Code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action#run-custom-codes) and [how to create a condition](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition) for more information.

```c
HyperDbg> !monitor rw fffff800`4ed60000 fffff800`4ed60100 code {90 90 90} condition {90 90 90}
```

{% hint style="success" %}
Keep in mind that a conditional event can be used in **Breaking to Debugger** and **Running Script** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

As **EventType** use  `HIDDEN_HOOK_READ` in the case you want just reads, use `HIDDEN_HOOK_WRITE` in the case you want just writes and use `HIDDEN_HOOK_READ_AND_WRITE` in the case you want both reads and writes and send the start address \(**from address**\) of where you want to monitor in `OptionalParam1`and end address \(**to address**\) of where you want to monitor in `OptionalParam2`address  **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !monitor](https://docs.hyperdbg.com/design/features/vmm-module/design-of-monitor)" to see how does it work.

### **Remarks**

{% hint style="danger" %}
You shouldn't use any of **!monitor**, **!epthook**, **bp**, and **!epthook2** commands on the same page \(4KB\) simultaneously. For example, when you put a hidden hook \(**!epthook2**\) on **0x10000005**, ****you shouldn't use any of **!monitor** or **!epthook** or **bp** commands on the address starting from **0x10000000** to **0x10000fff**.

 You can use **!epthook** or **bp** \(just _**!epthook**_ not **!epthook2** and not **!monitor**\) on two or more addresses on the same page \(means that you can use the **!epthook** or **bp** multiple times for addresses between a single page or putting multiple hidden breakpoints on a single page\). But you can't use **!monitor** or **!epthook2** twice on the same page.
{% endhint %}

This is an event command, but in the current version of HyperDbg \(in Debugger Mode\), this command will continue the debuggee for some time; however, you can use [this trick](https://docs.hyperdbg.com/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode) to make sure you won't lose any event.

### Requirements

Post-Nehalem Processor \(EPT\)

### Related

None


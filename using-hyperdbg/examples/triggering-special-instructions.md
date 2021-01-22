---
description: 'A description about hooking RDTSC, RDTSCP, I/O IN & OUT, RDPMC, etc.'
---

# Triggering Special Instructions

There are some special instructions in x86 and AMD64 processors that perform special tasks.

For example, we might be interested in the execution of I/O instruction \(IN & OUT\). We can monitor memory-mapped I/O using [!monitor](https://docs.hyperdbg.com/commands/extension-commands/monitor) command but for I/O mapped devices, we can use [!ioin](https://docs.hyperdbg.com/commands/extension-commands/ioin) and [!ioout](https://docs.hyperdbg.com/commands/extension-commands/ioout) commands.

{% hint style="danger" %}
Do not try to monitor all I/O ports or I/O port for the serial device that connected to the debugger if you are operating on Debugger Mode.
{% endhint %}

Using these commands, we can monitor I/O ports. For examples, let say we want to monitor

```c
HyperDbg> !ioin 0x3f8
```

If we want to monitor port 0x3f8, we use the following command.

```c
HyperDbg> !ioout 0x3f8
```

 Let's intercept another instruction. We know that **CPUID** is an important instruction that tries to get processor features, to see if a processor supports a special feature or not.

For example, we want to intercept all the CPUIDs that a process with process ID 490 tries to execute.

```c
HyperDbg> !cpuid pid 490
```

You can also break on the execution of other instructions like:

* RDTSC and RDTSCP using [!tsc](https://docs.hyperdbg.com/commands/extension-commands/tsc) command
* RDPMC using [!pmc](https://docs.hyperdbg.com/commands/extension-commands/pmc) command
* VMCALLs using [!vmcall](https://docs.hyperdbg.com/commands/extension-commands/vmcall) command


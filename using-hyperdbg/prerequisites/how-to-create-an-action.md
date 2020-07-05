---
description: This document helps you to create an action for events
---

# How to create an action?

[Actions ](https://docs.hyperdbg.com/design/debugger-internals/actions)are the most essential part of the [events](https://docs.hyperdbg.com/design/debugger-internals/events).

Each event consists of zero or multiple actions. An event with zero actions is treated as a disabled event.

There are three types of actions in HyperDbg as described [here](https://docs.hyperdbg.com/using-hyperdbg/prerequisites).

You can have multiple "**run custom codes**", "**log the states**", and "**break to the debugger**".

This document is a brief of how to create actions for an event.

### Break to the debugger

Break to the debugger, works exactly like classic debuggers like Windbg.

### Run custom codes

**Run custom code** gives you the ability to run your custom assembly codes whenever a special event is triggered; this option is fast and powerful as you can customize the HyperDbg based on your needs.

{% hint style="danger" %}
Accessing random memory in **custom code** and **condition code** is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)". You have some limitations on accessing memory on some special events.
{% endhint %}

#### Run custom code without a safe buffer

Each command in HyperDbg that are tagged as "**event**" in the documentation, follows the same structure described [here](https://docs.hyperdbg.com/design/debugger-internals/events). At the time you execute a command, you can add a `code { xx xx xx xx }` where `xx` is the assembly \(hex\) of what you want to be executed in the case of that event.

Generally, the assembly `code` in the code block will be called in the following form.

```c
typedef PVOID
DebuggerRunCustomCodeFunc(PVOID PreAllocatedBufferAddress, PGUEST_REGS Regs, PVOID Context);
```

As it called in the fastcall calling convention, **PreAllocatedBufferAddress** will be on `rcx`, **Regs** will be on `rdx` and **Context** is on `r8`.

**PreAllocatedBufferAddress** is the address of a non-paged safe buffer which is passed to the function on `rcx`. \(more about it later\).

**Regs**, for general-purpose registers, we pass a pointer to the following structure as the second argument on `rdx`. 

```cpp
typedef struct _GUEST_REGS
{
    ULONG64 rax; // 0x00
    ULONG64 rcx; // 0x08
    ULONG64 rdx; // 0x10
    ULONG64 rbx; // 0x18
    ULONG64 rsp; // 0x20 
    ULONG64 rbp; // 0x28
    ULONG64 rsi; // 0x30
    ULONG64 rdi; // 0x38
    ULONG64 r8;  // 0x40
    ULONG64 r9;  // 0x48
    ULONG64 r10; // 0x50
    ULONG64 r11; // 0x58
    ULONG64 r12; // 0x60
    ULONG64 r13; // 0x68
    ULONG64 r14; // 0x70
    ULONG64 r15; // 0x78
} GUEST_REGS, *PGUEST_REGS;
```

The **Context** is a special variable that shows an essential parameter of the event. This value is different for each event, you should check the documentation of that command for more information about the `Context`. For example, `Context` for **!syscall** command is the syscall-number or for the **!epthook2** command is the physical address of where the hidden hook triggered. Context is passed to the custom code as the third argument on `r8` . 

{% hint style="warning" %}
**PreAllocatedBufferAddress \(rcx\)** is always _NULL_ in **Run custom code without a safe buffer** and it's used in **Run custom code with a safe buffer**.
{% endhint %}



#### Run custom code with a safe buffer

The difference between "**Run custom code without a safe buffer**" and "**Run custom code without a safe buffer**" is that you have an extra parameter, called `buffer xx` where `xx` is the hex length of the buffer.



### Log the states 




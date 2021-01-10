---
description: This document helps you to create an action for events
---

# How to create an action?

[Actions ](https://docs.hyperdbg.com/design/debugger-internals/actions)are an essential part of the [events](https://docs.hyperdbg.com/design/debugger-internals/events).

Each event consists of zero or multiple actions. An event with zero actions is treated as a disabled event.

There are three types of actions in HyperDbg as described [here](https://docs.hyperdbg.com/using-hyperdbg/prerequisites).

You can have multiple "**Custom Codes**", "**Script**", and "**Break**".

This document is a brief of how to create actions for an event.

### Break

Break to the debugger, works exactly like classic debuggers like Windbg.

If you simply use a command without any extra parameters, it will be treated like classic debuggers, and HyperDbg gives the system control to the debugger.

### Script

### Custom Codes

**Run custom code** lets you run your custom assembly codes whenever a special event is triggered; this option is fast and powerful as you can customize the HyperDbg based on your needs.

{% hint style="danger" %}
Accessing random memory in **custom code** and **condition code** is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)". You have some limitations on accessing memory on some special events.
{% endhint %}

#### Run custom code without a safe buffer

Each command in HyperDbg that are tagged as "**event**" in the document follows the same structure described [here](https://docs.hyperdbg.com/design/debugger-internals/events). At the time you execute a command, you can add a `code { xx xx xx xx }` where `xx` is the assembly \(hex\) of what you want to be executed in the case of that event.

Generally, the assembly `code` in the code block will be called in the following form.

```c
typedef PVOID
DebuggerRunCustomCodeFunc(PVOID PreAllocatedBufferAddress, PGUEST_REGS Regs, PVOID Context);
```

As it called in the fastcall calling convention, **PreAllocatedBufferAddress** will be on `rcx`, **Regs** will be on `rdx` and **Context** is on `r8`.

**PreAllocatedBufferAddress** is the address of a non-paged safe buffer, which is passed to the function on `rcx`. \(more about it later\).

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

The **Context** is a special variable that shows an essential parameter of the event. This value is different for each event. You should check the documentation of that command for more information about the `Context`. For example, `Context` for **!syscall** command is the syscall-number or for the **!epthook2** command is the physical address of where the hidden hook triggered. Context is passed to the custom code as the third argument on `r8` . 

{% hint style="warning" %}
**PreAllocatedBufferAddress \(rcx\)** is always _NULL_ in **Run custom code without a safe buffer**, and it's used in **Run custom code with a safe buffer**.
{% endhint %}

As an example, we want to find the _TAG_ \(**ExAllocatePoolWithTag**\). If the tag is a special value, then we want to change it to a new value.

As you, ExAllocatePoolWithTag in Windows is defined as:

```c
PVOID ExAllocatePoolWithTag(
  POOL_TYPE                                      PoolType,
  SIZE_T                                         NumberOfBytes,
  ULONG                                          Tag
);
```

Based on the x64 calling convention, the parameters are passed as **rcx**, **rdx**, **r8**, **r9,** and stack and _Tag_ is on **r8**.

As you know, if you want to change a register in the target OS, you have to find the register in _Regs_ and change it from there. Based on `_GUEST_REGS`, **r8** is on **0x40** from the top of this structure.

Take a look at the following assembly code. It first checks whether the _Tag_ \(**r8**\) is **HDBG**, and if it's **HDBG**, then we change it to **HDB2**.

![](../../.gitbook/assets/actioncodeexample2.png)

When we convert the above code to assembly, then we have the following code :

```c
0:  48 8b 5a 40             mov    rbx,QWORD PTR [rdx+0x40]
4:  48 81 fb 48 44 42 47    cmp    rbx,0x47424448
b:  74 02                   je     f <ChangeIt>
d:  eb 08                   jmp    17 <Return>
000000000000000f <ChangeIt>:
f:  48 c7 42 40 48 44 42    mov    QWORD PTR [rdx+0x40],0x32424448
16: 32
0000000000000017 <Return>:
17: c3                      ret
```

Imagine, the **ExAllocatePoolWithTag** is located at ``fffff800`4ed6f010``. We can hook and change the Tag using the following command.

```c
HyperDbg> !epthook2 fffff800`4ed6f010 code {488B5A404881FB484442477402EB0848C7424048444232C3}
```

#### Run custom code with a safe buffer

The difference between "**Run custom code without a safe buffer**" and "**Run custom code without a safe buffer**" is that you have an extra parameter, called `buffer xx` where `xx` is the hex length of the buffer.

{% hint style="danger" %}
The **PreAllocatedBufferAddress** is just one buffer. You have to know how many cores you have. If there are two or more cores that might use the buffer simultaneously, you have to use a special location \(offset from the top of the buffer\) for each core to avoid race conditions and unintended behavior.
{% endhint %}

You can use the buffer which is available in `rcx`.


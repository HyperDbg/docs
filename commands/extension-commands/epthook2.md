---
description: Description of '!epthook2' command in HyperDbg.
---

# !epthook2 \(hidden hook with EPT - detours\)

### Command

> !epthook2

### Syntax

> !epthook2 \[address\] \[event options\]

### Description

In-line, detours-style kernel EPT hidden hook. \(fast\)

{% hint style="info" %}
This implementation of hidden hook won't cause vm-exit when it triggers, it's like detours and everything is done in vmx non-root, so it's much faster than !epthook but it some limitations, see **Remarks** for more information.
{% endhint %}

### Parameters

\[address\]

          The **Virtual** address of where we want to start to disassemble its memory

\[event options\]

          Regular event parameters used in HyperDbg events. \(For more information read [this ](https://docs.hyperdbg.com/using-hyperdbg/prerequisites)topic\)

### Context

As the **Context** \(**`r8`** in custom code and **`rdx`** in condition code register\) to the event trigger, we send the **physical** address of where put the hidden hook's breakpoint.

### Debugger

This event supports three debugging mechanisms.

* Break to Debugger
* Log the States
* Run Custom Code

{% hint style="info" %}
Please read  "[How to create a condition?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition)" if you need a conditional event, a conditional event can be used in all "**Break to Debugger**", "**Log the State**" and "**Run Custom Code**".
{% endhint %}

### Break to Debugger

Imagine we want to put a hook on ``fffff800`4ed6f010``, this will breaks to the debugger when the target address hits and gives the control back to you.

```c
!epthook2 fffff800`4ed6f010 
```

### Log the States

Not Completed Yet !

### Run Custom Code

Please read  "[How to create an action?](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-an-action)" for getting idea about how to run custom buffer code in **HyperDbg**.

{% hint style="warning" %}
Your custom code will be executed in vmx non-root. Take a look at [this topic](https://docs.hyperdbg.com/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode) for more information. Running code in vmx-root is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".
{% endhint %}

#### Run Custom Code \(Unconditional\)

As an example, we want to find the _TAG_ \(**ExAllocatePoolWithTag**\) if the tag is a special value then we want to change it to a new value.

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

Take a look at the following assembly code, it first checks whether the _Tag_ \(**r8**\) is **HDBG** and if it's **HDBG** then we change it to **HDB2**.

![](../../.gitbook/assets/actioncodeexample2.png)

When we convert the above code to assembly then we have the following code :

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
!epthook2 fffff800`4ed6f010 code {488B5A404881FB484442477402EB0848C7424048444232C3}
```

#### Run Custom Code \(Conditional\)

In the above example, we add a condition like this:

Imagine we want to check for the name of the process so only and only if the name contains the "**svchost.exe**" then trigger the event's action\(s\).

We all know that you can search for the name of the process in its `_EPROCESS`.

![](../../.gitbook/assets/imagefilenameoffset.png)

For example, **ImageFileName** in **\_EPROCESS** contains the 15 characters of the process name. It is not where Windows shows the name in Task Manager but checking this value is enough.

The following assembly code, gets the current `_KTHREAD` from `_KPCR`. From there we can find the address of `_KPROCESS` and this structure is located at the start address of `_EPROCESS`. 

As you can see from the above picture, **ImageFileName** is located at `+0x450` after the `_EPROCESS`.

So our final assembly code is like this : 

![](../../.gitbook/assets/assembly.png)

{% hint style="warning" %}
The offsets of `_EPROCESS`and other structures might change in the different versions of Windows. 
{% endhint %}

Now we should assemble the above code into its hex representation in the assembly. For example, you can use an [online assembler](http://defuse.ca/online-x86-assembler.htm). 

Keep in mind that if you return with `rax=0` or `null` then it means **false** and if your return anything other than zero \(for example `rax=1`\) then it means **true**.

If you return true then all the actions of that event will be executed and if you return **false**, then **HyperDbg** ignores the actions of that event.

The final result of the assembler is :

```c
0:  65 48 8b 04 25 88 01    mov    rax,QWORD PTR gs:0x188
7:  00 00
9:  48 8b 80 b8 00 00 00    mov    rax,QWORD PTR [rax+0xb8]
10: 48 8b 80 50 04 00 00    mov    rax,QWORD PTR [rax+0x450]
17: 48 b9 73 76 63 68 6f    movabs rcx,0x2e74736f68637673
1e: 73 74 2e
21: 48 39 c8                cmp    rax,rcx
24: 74 05                   je     2b <ReturnTrue>
26: 48 31 c0                xor    rax,rax
29: eb 07                   jmp    32 <Return>
000000000000002b <ReturnTrue>:
2b: 48 c7 c0 01 00 00 00    mov    rax,0x1
0000000000000032 <Return>:
32: c3                      ret
```

Now you can call the command with the following arguments \(condition code added to the above example\) :

```c
!epthook fffff801deadbeef code {488B5A404881FB484442477402EB0848C7424048444232C3} condition {65488B042588010000488B80B8000000488B805004000048B9737663686F73742E4839C874054831C0EB0748C7C001000000C3}
```

{% hint style="success" %}
Keep in mind, a conditional event can be used in **Break to Debugger** and **Log the State** too.
{% endhint %}

### IOCTL

This command uses the same method to [send IOCTL for regular events](https://docs.hyperdbg.com/design/debugger-internals/ioctl-requests-for-events). 

Use `HIDDEN_HOOK_EXEC_DETOURS` as **EventType**, ****and send the address of where you want to hook in `OptionalParam1`in **DEBUGGER\_GENERAL\_EVENT\_DETAIL**.

### Design

Take a look at "[Design of !epthook2](https://docs.hyperdbg.com/design/features/design-of-epthook2)" to see how does it work.

### **Remarks**

This command is much faster than **!epthook**, but it has the following limitations:

* It can be used only in kernel addresses, which means that you cannot use it for user-mode addresses.
* You can only use one hook in a page of memory, for example, if you put a hook on `fffff80126551006` then you cannot put another hook in the range of `fffff80126551000` to `fffff80126551fff`  because it's within the same page \(`0x1000` or `4096` bytes\).
* It has the limitation of classic detours hooks, we patch **18** bytes for our detours hook so when you put a hook anywhere in your assembly, you have to make sure that there is no relative jump or relative call within **18** bytes after the hook address. Most of the time, the start address of the function is detours-compatible \(doesn't start with relative jumps or relative calls\), especially in x64 fast call functions thus start address of a function is a good point to put these hidden hooks.
* `r11` is modified in the hook so you need to make sure that the target address doesn't use this register \(at least at that point of the program\). If you need the `r11` compile the HyperDbg with another register \(change the register in `EptHookWriteAbsoluteJump` and `EptHookWriteAbsoluteJump2`in `Ept.c`\).

### Requirements

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

[!epthook \(hidden hook with EPT - stealth breakpoints\)](https://docs.hyperdbg.com/commands/extension-commands/epthook)


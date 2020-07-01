---
description: This document helps you to create a condition for events
---

# How to create a condition?

HyperDbg supports to create conditional events, each event has one [condition ](https://docs.hyperdbg.com/design/debugger-internals/conditions)and can have multiple [actions](https://docs.hyperdbg.com/design/debugger-internals/actions).

An unconditional event is an event that all its actions will be executed without any condition.

This document is a brief of how to create a conditional event.

Each command in HyperDbg that we call it an "event", follows the same structure described [here](https://docs.hyperdbg.com/design/debugger-internals/events). At the time you execute a command, you can add a `condition { xx xx xx xx }` where `xx` is the assembly \(hex\) of what you want to be executed in the case of that event.

For example, let's imagine we want to create a condition for a command like "**!epthook**".

When you execute the command like :

```c
!epthook fffff801deadbeef 
```

then it is unconditional but when you execute a command like this :

```c
!epthook fffff801deadbeef condition { 90 90 90 90 }
```

then it is a conditional command.

{% hint style="success" %}
Note that you can use all of the events in the same way \(instead of **!epthook**\), for example, you can use **!syscall, !sysret, !epthook2, !ioin** and etc.
{% endhint %}

### Example 1

Imagine we want to check for the name of the process and only and only if the name contains the "svchost.exe" then trigger the event's action\(s\).

We all know that you can search for the name of the process in its `_EPROCESS`.

![](../../.gitbook/assets/imagefilenameoffset.png)

For example, **ImageFileName** in **\_EPROCESS** contains the 15 characters of the process name. It is not where Windows shows the name in Task Manager but checking this value is enough.

Imagine the following assembly code that gets the current `_KTHREAD` from `_KPCR`. From there we can find the address of `_KPROCESS` and this structure is located at the start address of `_EPROCESS`. 

As you can see from the above picture, **ImageFileName** is located at `+0x450` after the `_EPROCESS`.

So our final assembly code is like this : 

![](../../.gitbook/assets/assembly.png)

{% hint style="warning" %}
The offsets of EPROCESS and other structures might change in the different versions of Windows. 
{% endhint %}

Now we should assemble the above code into its hex representation in the assembly. For example, you can use an [online assembler](http://defuse.ca/online-x86-assembler.htm). 

Keep in mind that if you return with `rax=0` or `null` then it means **false** and if your return anything other than zero \(for example `rax=1`\) then it means **true**.

If you return true then all the actions of that event will be executed and if you return **false**, then HyperDbg ignores the actions of that event.

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

Now you can call the command with the following arguments :

```c
!epthook fffff801deadbeef condition {65488B042588010000488B80B8000000488B805004000048B9737663686F73742E4839C874054831C0EB0748C7C001000000C3}
```

or

```c
!syscall condition {65488B042588010000488B80B8000000488B805004000048B9737663686F73742E4839C874054831C0EB0748C7C001000000C3}
```

We automatically add a `0xc3` or `ret` opcode to the end of the condition assembly and in the case if you forget to return the control of the processor back to the HyperDbg, then there is no problem. Make sure to not jump to another address without returning back to the HyperDbg, otherwise it causes a crash on your system.

### Example 2

Sometimes we need




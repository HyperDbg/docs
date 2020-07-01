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

```text
!epthook fffff801deadbeef 
```

then it is unconditional but when you execute a command like this :

```text
!epthook fffff801deadbeef condition { 90 90 90 90 }
```

then it is a conditional command.

{% hint style="success" %}
Note that you can use all of the events in the same way \(instead of **!epthook**\), for example, you can use **!syscall, !sysret, !epthook2, !ioin** and etc.
{% endhint %}

### Examples

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

Keep in mind that if you return with rax=0 then it means false and if your return anything other than zero \(for example rax=1\) then it means true.


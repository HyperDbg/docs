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

Examples


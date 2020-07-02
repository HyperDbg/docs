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

#### Run custom code without a safe buffer

Each command in HyperDbg that are tagged as "**event**" in the documentation, follows the same structure described [here](https://docs.hyperdbg.com/design/debugger-internals/events). At the time you execute a command, you can add a `code { xx xx xx xx }` where `xx` is the assembly \(hex\) of what you want to be executed in the case of that event.



#### Run custom code with a safe buffer

Accessing random memory in **custom code** and **condition code** is considered "[unsafe](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior)".



### Log the states 




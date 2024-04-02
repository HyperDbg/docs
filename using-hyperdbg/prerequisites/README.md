---
description: >-
  This document contains essential notes that you should know before starting
  with HyperDbg
---

# Prerequisites

In HyperDbg, there is a fundamental term called "**event**". Each time you use HyperDbg, you set a special event; for instance, imagine you want to intercept system-calls. You should set up an event that will be triggered in the case of a syscall execution, or when you set a hidden hook event on a particular function.

Whenever these events are triggered, HyperDbg performs specific actions that are configured by you.

#### What are HyperDbg's actions?

HyperDbg has three types of actions. These actions are **Break**, **Script**, **Custom Codes**.

**Break** is exactly like classic-debuggers, where all of the cores are paused and won't run any instruction without the debugger's permission. (This feature is not available in local debugging).

**Script** is a feature that helps you view the parameters, registers, and memory without breaking into the debugger and running your custom script. You can use it when you want to analyze your target, and it creates the logs or run codes in the kernel.

It's much faster than exiting to the debugger and continues from there as all of these logging mechanisms are done in the kernel, and you see the messages in user-mode.

**Custom Codes** gives you the ability to run your custom assembly codes whenever a special event is triggered; this option is fast and powerful as you can customize the HyperDbg based on your needs.

#### What are the conditions?

Each event will be triggered only in two situations. First, you set a particular condition for the event (**conditionally**), so each time the event is triggered, and the condition is met, then your selected action is performed.

The second situation is when you didn't put any conditions (**unconditionally**), so it will be triggered when that event happens, and there is no condition check.

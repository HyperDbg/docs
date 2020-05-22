---
description: >-
  This document contains essential notes that you should know before starting
  with HyperDbg
---

# Prerequisites

  
In HyperDbg, there is a fundamental term called "**event**". Each time you use HyperDbg, you set a special event, for instance, imagine you want to intercept system-calls, you should set up an event that will be triggered in the case of a "syscall" execution, or when you set a hidden hook event on a particular function.

Whenever these events triggered, HyperDbg performs specific actions that are configured by you.

#### What are HyperDbg's actions?

HyperDbg has three types of actions. These actions are **Break to Debugger**, **Log the States**, **Execute Custom Codes**.

**Break to Debugger** is exactly like classic-debuggers, where all of the cores are paused and won't run any instruction without the debugger's permission. \(This feature is not available in local debugging\).

**Log the States** is a feature that helps you to log the parameters, registers, and memory without breaking into the debugger. You can use it when you want to analyze your target, and it creates the logs in the kernel.

It's much faster than exiting to the debugger and continues from there as all of these logging mechanisms are done in the kernel, and you just see the messages in user-mode.

**Execute Custom Codes** gives you the ability to run your custom assembly codes whenever a special event is triggered; this option is fast and powerful as you can customize the HyperDbg based on your needs.

#### What are the conditions?

Each event will be triggered only in two situations. First, you set a particular condition for the event \(**conditionally**\), so each time the event is triggered, and the condition is met, then your selected action is performed.

The second situation is when you didn't put any conditions \(**unconditionally**\), so it will be triggered each time when that event happens, and there is no condition check.


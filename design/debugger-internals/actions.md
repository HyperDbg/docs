---
description: What is actions in HyperDbg & how to use them?
---

# Actions

## What are the actions?

Actions define the operation that the debugger should do in the case of triggering an event. In other words, events are containers of actions.

Each event can have zero or many actions. In an unconditional event, all actions are performed one by one with the insertion order, and in conditional events, actions are performed only and only if the condition is met.

## Types of Actions

HyperDbg is not a classic debugger, so we can have multiple kinds of operations in events.

Actions can be defined in 3 different types, break, script, and custom-code.

1. **Break** : Exactly like other classic debuggers, this type of action halts the system and makes the system available for future user commands. This type of action is only available in debugging a remote machine. It is because you cannot halt your current local system.
2. **Script** : This action type is a special feature that creates a log from the registers, memory, and special details (pseudo-registers) without halting the system and transfers the logs from kernel mode and vmx-root mode, safely to the debugger user mode and also you can call predefined functions and change the state of the system directly. You can use this type of action in both debugging a remote machine and debugging a local machine.
3. **Custom Code** : Running a custom code is a special feature that will allow you to execute your custom assembly codes in the case of triggering an event. This means that your assembly codes will be executed, and the results will be returned safely to the debugger user mode. You can use this type of action in both debugging a remote machine and debugging a local machine.

#### Break

By default and if you don't specify any parameters like `script { }` or `code { }` then HyperDbg interprets events as a **break**. It means that every time this event is triggered, then the system or the target process is completely halted, and now you can control the system. It is exactly like other debuggers like Windbg.

#### Script

Script-engine is a powerful feature of HyperDbg that makes you able to create easy statements to create logs from the system state, change the system state, and call pre-defined functions and even halt the system and give its control to the debugger.

This feature never halts the system, and HyperDbg's script engine is vmx-root compatible; however, you should avoid [unsafe behavior](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior).

When you use `script { }` in your events, then you are using the script-engine.

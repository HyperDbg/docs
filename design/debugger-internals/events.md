---
description: What are events in HyperDbg & how to use them?
---

# Events

### What are the events?

Events are an essential concept in HyperDbg. Almost all of the HyperDbg features are developed as an event.

**Event** is the occurrence of an incident that is of interest to the debugger. This comprises a wide range of activities ranging from a specific system call (syscall) that the debugger is set to monitor, to access to a particular memory address. HyperDbg can be configured to perform arbitrarily defined actions upon the occurrence of each event.

For example, hidden hooks are an event. When this event is triggered, your actions will be performed, or a syscall hook is an event. Whenever the system executes an **SYSRET** instruction or a **SYSCALL** instruction, then the event is triggered.

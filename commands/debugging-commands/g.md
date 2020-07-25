---
description: Description of 'g' command in HyperDbg.
---

# g \(continue debugging or processing kernel packets\)

### Command

> g

### Syntax

> g

### Description

When the **HyperDbg** debugger is paused by [pause command](https://docs.hyperdbg.com/commands/debugging-commands/pause) or CTRL+C or CTRL+BREAK, you can continue debugging using this command. You can use this command in both **local** and **remote** debugging.

### Parameters

None

### IOCTL

None

### **Remarks**

In remote debugging [pause command](https://docs.hyperdbg.com/commands/debugging-commands/pause) or CTRL+C or CTRL+BREAK causes, the target system to pause completely and the target system halts. 

In local debugging [pause command](https://docs.hyperdbg.com/commands/debugging-commands/pause) or CTRL+C or CTRL+BREAK causes the debugger to stop processing kernel and vmx packets \(they won't stop working, just we ignore the messages\). Also, we don't save messages so when you pause the debugger then the logs from the events are simply ignored and you can't process them in the future.

### Requirements

None

### Related

[pause \(break to the debugger and pause processing kernel packets\)](https://docs.hyperdbg.com/commands/debugging-commands/pause)




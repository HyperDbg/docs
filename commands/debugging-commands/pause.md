---
description: Description of 'pause' command in HyperDbg.
---

# pause \(break to the debugger and pause processing kernel packets\)

### Command

> pause

### Syntax

> pause

### Description

On the remote system, this command halts the system and gives control of the remote system to the debugger, in local debugging, this command stops processing kernel and vmx packets and ignores them. 

{% hint style="info" %}
You can unpause using [g command](https://docs.hyperdbg.com/commands/debugging-commands/g).
{% endhint %}

### Parameters

None

### IOCTL

None

### **Remarks**

In remote debugging pause command or CTRL+C or CTRL+BREAK causes, the target system to pause completely and the target system halts. 

In local debugging pause command or CTRL+C or CTRL+BREAK causes the debugger to stop processing kernel and vmx packets \(they won't stop working, just we ignore the messages\). Also, we don't save messages so when you pause the debugger then the logs from the events are simply ignored and you can't process them in the future.

### Requirements

None

### Related

[.script \(run batch script commands\)](https://docs.hyperdbg.com/commands/meta-commands/.script)

[g \(continue debugging or processing kernel packets\)](https://docs.hyperdbg.com/commands/debugging-commands/g)

[settings \(configures different options and preferences\)](https://docs.hyperdbg.com/commands/debugging-commands/settings)




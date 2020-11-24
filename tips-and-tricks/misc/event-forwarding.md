---
description: Brief explanation about Event Forwarding Mechanism
---

# Event Forwarding

Event forwarding is a feature that is designed to make HyperDbg a tool for log gathering and analyzing system behavior. This way you can use HyperDbg for [\#DFIR](https://twitter.com/search?q=%23dfir) purposes.

You can use event forwarding if you want to forward the event monitoring result from your internal system to an external source e.g. **File**, **NamedPipe**, or **TCP Socket**.

Thanks a lot to [Samir](https://twitter.com/SBousseaden) for his valuable suggestions that make this feature available.

### Output command

A new command called "[output](https://docs.hyperdbg.com/commands/debugging-commands/output)" was added to the HyperDbg to create remote sources.

In order to use event forwarding, you should perform the following steps, all of them are done using the [output ](https://docs.hyperdbg.com/commands/debugging-commands/output)command.

First, you should create an output source.

Second, you should open the remote source.

Third, you can use your event source in all of the events.

Fourth, you should close the source when you no longer want to use the source.

### Output argument on event

When you create your event, then you can specify  `output { OutputName }` to your event and use the event forwarding.

HyperDbg supports multiple output sources, which means that you can send the result of a single event to multiple locations.

You can create unlimited output sources but you can use a limited number of output sources for a single event.

Check the [output ](https://docs.hyperdbg.com/commands/debugging-commands/output)command to see more examples.

### Remarks

Event forwarding is a one-way mechanism. This means you can just see the logs from the client and you cannot make changes to the logs or send commands to the target client. If you want to control the debugger from a remote system, you can use [.listen](https://docs.hyperdbg.com/commands/meta-commands/.listen) and [.connect](https://docs.hyperdbg.com/commands/meta-commands/.connect) commands.

### Examples

The following repository contains some examples of listening on a named pipe as a server or listening on TCP sockets to use event forwarding.

{% embed url="https://github.com/HyperDbg/event-forwarding-examples" %}

Assume that we want to send the results of syscall \(Syscall numbers in rax\) to several sources.




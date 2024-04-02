---
description: Brief explanation about Event Forwarding Mechanism
---

# Event forwarding

Event forwarding is a feature designed to make HyperDbg a tool for log gathering and analyzing system behavior. This way, you can use HyperDbg for [#DFIR](https://twitter.com/search?q=%23dfir) purposes.

You can use event forwarding to forward the event monitoring result from your internal system to an external source, e.g., **File**, **NamedPipe**, or **TCP Socket**.

Thanks a lot to [Samir](https://twitter.com/SBousseaden) for his valuable suggestions that make this feature available.

## Output command

A new command called "[output](https://docs.hyperdbg.org/commands/debugging-commands/output)" was added to the HyperDbg to create remote sources.

In order to use event forwarding, you should perform the following steps. All of them are done using the [output ](https://docs.hyperdbg.org/commands/debugging-commands/output)command.

First, you should create an output source.

Second, you should open the remote source.

Third, you can use your event source in all of the events.

Fourth, you should close the source when you no longer want to use the source.

## Output argument on event

When you create your event, then you can specify `output { OutputName }` to your event and use the event forwarding.

HyperDbg supports multiple output sources, which means that you can send the result of a single event to multiple locations.

You can create unlimited output sources, but you can use a limited number of output sources for a single event.

Check the [output ](https://docs.hyperdbg.org/commands/debugging-commands/output)command to see more examples.

### Remarks

Event forwarding is a one-way mechanism. This means you can just see the client's logs, and you cannot make changes to the logs or send commands to the target client. If you want t,o control the debugger from a remote system, you can use [.listen](https://docs.hyperdbg.org/commands/meta-commands/.listen) and [.connect](https://docs.hyperdbg.org/commands/meta-commands/.connect) commands.

## Format (plain-text and JSON)

Event forwarding is only applied to the script, which means that you can use the [**print** ](https://docs.hyperdbg.org/commands/scripting-language/functions/exports/print)and the [**printf**](https://docs.hyperdbg.org/commands/scripting-language/functions/exports/printf) function to generate results that will be passed to the target output source.

It's possible to create JSON results using the [**printf**](https://docs.hyperdbg.org/commands/scripting-language/functions/exports/printf) function.

### Examples

The following repository contains some examples of listening on a named pipe as a server or listening on TCP sockets to use event forwarding.

{% embed url="https://github.com/HyperDbg/event-forwarding-examples" %}

Assume that we want to send the results of syscall (syscall numbers in `rax`) to several sources. The following video shows how to redirect these events to the **file**, **TCP Socket**, **named pipe**.

## Demo

Watch the video - How to use event forwarding.

{% embed url="https://www.youtube.com/watch?v=tyapdCEZtic" %}

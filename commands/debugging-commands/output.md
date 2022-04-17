---
description: Description of the 'output' command in HyperDbg.
---

# output (create output source for event forwarding)

### Command

> output

### Syntax

> output \[create Name (string)] \[file|namedpipe|tcp Address (string)]&#x20;
>
> output \[open|close Name (string)]&#x20;

### Description

Create, open, or close a source output for event forwarding.

You can read more about **event forwarding** [here](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding).

### Parameters

**\[create Name (string)]**

The name of the instance output to be created.

**\[file|namedpipe|tcp Address (string)]**&#x20;

Type and address of the target resource.

**\[open|close Name (string)]**

The action of this command or whether this command tries to open an output source or close it combined with its name.

### Examples

The following command creates an output source, which is a `file` and the results of the command (script) will be saved into the `c:\users\sina\desktop\output.txt`.

```diff
HyperDbg> output create MyOutputName1 file c:\users\sina\desktop\output.txt
```

The following command creates an output source, which is a `tcp` and the results of the command (script) will be sent into the `192.168.1.10:8080`.

```diff
HyperDbg> output create MyOutputName2 tcp 192.168.1.10:8080
```

The following command creates an output source, which is a `namedpipe` and the results of the command (script) will be sent into the `\\.\Pipe\HyperDbgOutput`.

```diff
HyperDbg> output create MyOutputName3 namedpipe \\.\Pipe\HyperDbgOutput
```

You cannot use the above resources until you **open** them using the following command. This command opens an output source (named "**MyOutputName1**") that was previously created by the output's **create** parameter.

```diff
HyperDbg> output open MyOutputName1
```

When you finished using an output source, you can **close** it using the following command. Once you close an output source, you cannot use it anymore, and also, you **CANNOT** open it again, but once again, you can **create** a new source with the same address but with a different **name**.

```diff
HyperDbg> output close MyOutputName1
```

After creating and opening the output source, you can use its name in all HyperDbg events. You should pass `output {MyOutputName1}` when you are creating an event, for example [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall) is an event so that you can use it like this :

```diff
HyperDbg> !syscall script { print(@rax); } output {MyOutputName1}
```

It is also possible to send the results to several output sources. You should separate the output names with `,` .

For example :

```diff
HyperDbg> !syscall script { print(@rax); } output {MyOutputName1 , MyOutputName2 , MyOutputName3}
```

You can specify up to `5` output sources in the default build of HyperDbg, but if you need more output sources for a single event, then you should compile HyperDbg with different configurations as described on [Customize Build](https://docs.hyperdbg.org/tips-and-tricks/misc/customize-build) and change the `DebuggerOutputSourceMaximumRemoteSourceForSingleEvent`.

### IOCTL

None

### Remarks

You can use this command to forward the results of the scripts from all the [events](https://docs.hyperdbg.org/design/debugger-internals/events).

{% hint style="danger" %}
You **cannot** use event forwarding in the immediate messaging mode in events (`imm no`).
{% endhint %}

### Requirements

None

### Related

[Event Forwarding](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding)

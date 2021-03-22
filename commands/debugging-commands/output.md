---
description: Description of 'output' command in HyperDbg.
---

# output \(create output source for event forwarding\)

### Command

> output

### Syntax

> output \[ create \| open \| close \] \[ type \(file \| namedpipe \| tcp\) \] \[ name \| address \]

### Description

Create, open, or close a source output for event forwarding.

You can read more about **event forwarding** [here](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding).

### Parameters

**\[ create \| open \| close \]**

          The action of this command or whether this command tries to create a new output source or open an output source or close it.

**\[ type \(file \| namedpipe \| tcp\) \]**

          In the case of creating an event, this parameter shows the type of the event.

**\[ name \| address \]**

          In the case of "**create**", it shows the address of the remote source, and in the case of "**open**" or "**close**", it shows the name of the output source.

### Examples

The following command creates an output source, which is a `file` and the results of the command \(script\) will be saved into the `c:\users\sina\desktop\output.txt`.

```diff
HyperDbg> output create MyOutputName1 file c:\users\sina\desktop\output.txt
```

The following command creates an output source, which is a `tcp` and the results of the command \(script\) will be sent into the `192.168.1.10:8080`.

```diff
HyperDbg> output create MyOutputName2 tcp 192.168.1.10:8080
```

The following command creates an output source, which is a `namedpipe` and the results of the command \(script\) will be sent into the `\\.\Pipe\HyperDbgOutput`.

```diff
HyperDbg> output create MyOutputName3 namedpipe \\.\Pipe\HyperDbgOutput
```

You cannot use the above resources until you **open** them using the following command. This command opens an output source \(named "**MyOutputName1**"\) that was previously created by the output's **create** parameter.

```diff
HyperDbg> output open MyOutputName1
```

When you finished using an output source, you can **close** it using the following command. Once you close an output source, you cannot use it anymore, and also, you **CANNOT** open it again, but once again, you can **create** a new source with the same address but with a different **name**.

```diff
HyperDbg> output close MyOutputName1
```

After creating and opening the output source, you can use its name in all HyperDbg events. You should pass `output {MyOutputName1}` when you are creating an event, for example [!syscall](https://docs.hyperdbg.com/commands/extension-commands/syscall) is an event so that you can use it like this : 

```diff
HyperDbg> !syscall script { print(@rax); } output {MyOutputName1}
```

It is also possible to send the results to several output sources. You should separate the output names with `,` . 

For example :

```diff
HyperDbg> !syscall script { print(@rax); } output {MyOutputName1 , MyOutputName2 , MyOutputName3}
```

You can specify up to `5` output sources in the default build of HyperDbg, but if you need more output sources for a single event, then you should compile HyperDbg with different configurations as described on [Customize Build](https://docs.hyperdbg.com/tips-and-tricks/misc/customize-build) and change the `DebuggerOutputSourceMaximumRemoteSourceForSingleEvent`.

### IOCTL

None

### **Remarks**

You can use this command to forward the results of the scripts from all the [events](https://docs.hyperdbg.com/design/debugger-internals/events).

{% hint style="danger" %}
You **cannot** use event forwarding in the immediate messaging mode in events \(`imm no`\).
{% endhint %}

### Requirements

None

### Related

[Event Forwarding](https://docs.hyperdbg.com/tips-and-tricks/misc/event-forwarding)


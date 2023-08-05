---
description: The event calling stage in HyperDbg
---

# Event calling stage

Starting from HyperDbg **v0.5**, the event calling stages mechanism is added to the debugger.

This mechanism enables us to specify the exact stage at which HyperDbg will trigger the event. For instance, we can choose whether the event should be called before or after the emulation process takes place.

Most of the events in HyperDbg are based on emulation. For example, before or after modifying the memory (as a result of the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command). Another example is for different special instructions like **RDMSR**, **WRMSR**, **CPUID**, etc. These stages will notify HyperDbg whether the event should be triggered before the execution of these instructions or after the execution of these instructions.

### Different Calling Stages

HyperDbg allows you to specify one of the following calling stages for an event:

* **All**
* **Pre**
* **Post**

When you specify '**all**' for an event, it will be triggered both before and after the emulation process. On the other hand, if you choose '**pre**', the event will be triggered before the emulation, and if you select '**post**', it will be triggered after the emulation. If no event stage is explicitly specified, the default behavior is to trigger events in the '**pre**' calling stage.

The calling stages serve a valuable purpose in analyzing specific events such as '[!cpuid](https://docs.hyperdbg.org/commands/extension-commands/cpuid)', '[!msrread](https://docs.hyperdbg.org/commands/extension-commands/msrread)', '[!msrwrite](https://docs.hyperdbg.org/commands/extension-commands/msrwrite)', etc. For instance, by selecting the '**post**' calling stage, you can observe and potentially modify the results of the emulation after it has occurred.

One of the primary applications of these calling stages is seen in the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command. For instance, if you wish to be notified whenever memory is modified at a particular target address, you can utilize the '**pre**' calling stage to examine the memory's content before a **MOV** instruction, while the '**post**' calling stage reveals the content of the memory after executing the **MOV**, effectively showing the modified memory contents.

### Calling Stages and Event Short-circuiting

An important note is, once you [short-circuit or ignore an event](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting), the '**post**' calling stage will not be triggered as the emulation is completely ignored and in reality, there is no '**post**' stage.&#x20;

Another note is that short-circuiting events are not permitted in the 'post' stage. This is because, by the time the 'post' stage is reached, the emulation has already been performed, and there is nothing left to be ignored, making it illogical to apply short-circuiting at this point.

### Using Calling Stages in Events



### Finding Calling Stages in Scripts







a new $stage pseudo-register is added/

### Miscellaneous

Commands like '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' or '[!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2)' don't support calling stages. Due to the nature of function hooking implementing calling stages in this context wouldn't be meaningful.

If only one event short-circuits a special EPT hook or a special MSR read/write, or any other events, the emulation won't be performed and the '**post**' mode will be ignored for all of the same events.











\

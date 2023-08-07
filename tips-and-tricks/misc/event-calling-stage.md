---
description: The event calling stage in HyperDbg
---

# Event calling stage

Starting from HyperDbg **v0.5**, the event calling stages mechanism is added to the debugger.

This mechanism enables us to specify the exact stage at which HyperDbg will trigger the [event](https://docs.hyperdbg.org/using-hyperdbg/sdk/events). For instance, we can choose whether the event should be called before or after the emulation process takes place.

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

Event calling stages are determined by using the '**stage pre**', '**stage post**', or '**stage all**' in the definition of events.&#x20;

For example, you can specify the '**post**' stage for the following event commands:

```
0: kHyperDbg> !monitor rw nt!kd_default_mask nt!kd_default_mask+8 stage post
```

Or you can specify the '**all**' stage for them.

```
0: kHyperDbg> !msrwrite c0000082 stage all
```

### Finding Calling Stages in Scripts

In order to determine the calling stage, a new [pseudo-register](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers) called `$stage` is added. This pseudo-register can be either **0** which shows that the event is called in the '**pre**' calling stage or **1** which shows it's called in the '**post**' calling stage.&#x20;

Please be aware that the event invocation stage cannot be '**all**' since the event occurs either in the '**pre**' or '**post**' calling stage, and it cannot occupy both simultaneously.

The following example reads the '**$stage**' pseudo-register in case of an event and as the result is **1**, it means the event is called in the '**post**' calling stage.

```
HyperDbg> ? print($stage);
1
```

In cases where the debugger is halted without being triggered by an event (such as through a breakpoint, CTRL+C, stepping, etc.), the pseudo-register '**$stage**' will display a value of 0. However, it's important not to misinterpret this as a '**pre**' stage, as this pause isn't prompted by an actual event.

### Finding Calling Stages in Event Breaks

Once an event is triggered, the debugger will show the event id, as well as the event calling stage, for example, the following event is triggered in the '**post**' calling stage. Note the `(post)` in the debugger message.

<pre><code><strong>1: kHyperDbg> g
</strong>debuggee is running...
event 0x3 triggered (post)
</code></pre>

Or the following event is called the '**pre**' calling stage.

```
2: kHyperDbg> g
debuggee is running...
event 0x5 triggered (pre)
```

### Examples

Below are various examples demonstrating the usage of the event calling stages mechanism. You can apply this mechanism to events that support calling stages. Please refer to the documentation for each specific event to determine whether it supports calling stages or not.

#### Example 1

The following example shows how we can view the memory **before** and **after** modification by using the '**all**' calling stage and how the '**$stage**' pseudo-register is used to check for different stages.

```clike
!monitor w 7 ff71f118210 7 ff71f118210 + 4 stage all script {
  if ($stage == 1) {
    curr_memory = dq($context);
    printf("current memory: %llx\n", curr_memory);
  } else {
    prev_memory = dq($context);
    printf("thread id: %x , from (RIP): %llx modified address: %llx, previous memory: %llx",
      $tid, @rip, $context, prev_memory);
  }
}
```

#### Example 2

The following example shows how can we view (or possibly modify the registers) after running a **CPUID** instruction.

In this example, we checked whether the '**$context**' or the **EAX** register is equal to **1** and if it's equal to it, we mask the **ECX** register's **5th bit** (which is the [indicator of support for Intel VT-x](https://en.wikipedia.org/wiki/CPUID)). As a result, if you use a program like [CPU-Z](https://www.cpuid.com/), it no longer shows support for VT-x (just for the **CPUID** instruction, not disabling it completely).

```clike
!cpuid stage post script {
  if ($context == 1) {
    @ecx = @ecx & ~(1 << 5);
  }
}
```

#### Example 3

As described [here](https://www.felixcloutier.com/x86/syscall.html), **SYSCALL** saves **RFLAGS** into **R11** and then masks **RFLAGS** using the **IA32\_FMASK** MSR (MSR address **0xC0000084**); specifically, the processor clears in **RFLAGS** every bit corresponding to a bit that is set in the **IA32\_FMASK** MSR.

Using the following example we would be sure that if somewhere in the drivers, rootkits, or Windows, some codes try to modify this MSR register, the **9th** flag ([Interrupt Flag](https://en.wikipedia.org/wiki/FLAGS\_register)) will remain untouched.

```clike
!msrwrite 0xc000084 stage pre script {
    @eax = @eax | (1 << 9);
}
```

#### Example 4

The following example shows how we can use the '**post**' calling stage to view the **CR2** register as a result of a [page-fault](https://en.wikipedia.org/wiki/Page\_fault). Note that, the value of the `@cr2` is not valid in the '**pre**' calling stage.

```clike
!exception e stage post script {
  printf("page-fault happens at: %llx", @cr2);
}
```

### Miscellaneous

Commands like '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' or '[!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2)' don't support calling stages. Due to the nature of function hooking implementing calling stages in this context wouldn't be meaningful.

If only one event short-circuits a special EPT hook or a special MSR read/write, or any other events, the emulation won't be performed and the '**post**' mode will be ignored for all of the same events.

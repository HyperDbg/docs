---
description: Methods to disable or enable events when the debuggee is halted
---

# Enable and Disable Events in Debugger Mode

In the current versions of **HyperDbg** \(in [Debugger Mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode)\), some of the commands \(especially [events](https://docs.hyperdbg.com/design/debugger-internals/events)\) cannot be applied immediately.

This means that your commands will go through the routines from user-mode to kernel-mode and then vmx-root mode; thus, the debuggee is continued for some time, and then the debuggee is halted again.

It is clear that you'll lose the current context \(registers and memory\), and also your current process, which is under debugging, might find some time to continue its normal execution.

One important note about HyperDbg is that whenever you want to create an event \(run an "**event**" command\), HyperDbg continues the debuggee; however, all the other events \(active events\) are ignored till the current event successfully applied; thus, HyperDbg might ignore some of the events during this process.

Let's assume we triggered a breakpoint in our target process, and from now on, we want to monitor any syscall \(syscall = 0x55\) execution by that process. Clearly, if we use the [!syscall](https://docs.hyperdbg.com/commands/extension-commands/syscall) command, as it's an event, the debuggee will be continued, and we might lose some of the system-calls as we didn't apply the event \(syscall-hook\) immediately.

**What's the solution to solve these problems?**

Whenever the system is halted, you can run scripts in debuggee, and HyperDbg executes the script while it guarantees that the debuggee \(both user-mode processes and kernel-mode\) kept halted.

The solution to this problem is to create the event before trying to debug the debuggee target process. For example, we execute the following command to get the syscalls `0x55` from the process with `pid=0x490`.

```c
HyperDbg> !syscall 55 pid 490
```

After that, we can see the event number using the '[events](https://docs.hyperdbg.com/commands/debugging-commands/events)' command.

Note that the event number is `0` in this case.

```c
HyperDbg> events
0       (enabled)           !syscall 55 pid 490
```

Now, we disable the event using the following command.

```c
HyperDbg> events d 0
```

If we rerun the '[events](https://docs.hyperdbg.com/commands/debugging-commands/events)' command, we can see that the event `0` is in a disabled state.

```c
HyperDbg> events
0       (disabled)          !syscall 55 pid 490
```

Now, we try to debug our target process, and when we halt the debuggee in our target process, we can use the '[events](https://docs.hyperdbg.com/commands/debugging-commands/events)' command or '[EnableEvent](https://docs.hyperdbg.com/commands/scripting-language/functions/enableevent)' function \(from script-engine\) to re-enable the event.

Note that `0` is the event number which we get from the '[events](https://docs.hyperdbg.com/commands/debugging-commands/events)' command.

```c
HyperDbg> events e 0
```

or

```c
HyperDbg> ? EnableEvent(0);
```

After that, we receive the events relating to the event with event number `0`; thus, we won't lose any events as the guest remained paused while the above expression command is executed.

At last, if we want to disable the event, we can use the following commands.

```c
HyperDbg> events d 0
```

or

```c
HyperDbg> ? DisableEvent(0);
```

In the above examples, we used [**EnableEvent**](https://docs.hyperdbg.com/commands/scripting-language/functions/enableevent) and [**DisableEvent**](https://docs.hyperdbg.com/commands/scripting-language/functions/disableevent) functions of script-engine using the '[?](https://docs.hyperdbg.com/commands/debugging-commands/eval)' command.

{% hint style="danger" %}
**Important note**

If you are operating in [Debugger Mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode), you can enable or disable events while the debuggee is in a halt state. However, if you want to clear an event or all events then you lose the context as the debuggee is continued for some time to process the **clear** operation.
{% endhint %}

Using this way, we can solve the problem of losing some events in HyperDbg.


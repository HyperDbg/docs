---
description: Methods to disable or enable events when the debuggee is halted
---

# Enable and Disable Events in Debugger Mode

In the current versions of HyperDbg \(in [Debugger Mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode)\), some of the commands \(especially [events](https://docs.hyperdbg.com/design/debugger-internals/events)\), cannot be applied immediately. This means that your commands will go through the routines from user-mode to kernel-mode and then vmx-root mode; thus, the debuggee is continued for sometimes and then the debuggee is halted again. It is clear that you'll lose the current context \(registers and memory\) and also your current process which is under debugging might find sometimes to continue its normal execution.

Let's assume we triggered a breakpoint in our target process and from now on, we want to monitor any syscall execution by that process. Clearly, if we use the [!syscall](https://docs.hyperdbg.com/commands/extension-commands/syscall), as it's an event, the debuggee will be continued and we might lose some of the system-calls as we didn't apply the event \(syscall-hook\) immediately.

**So, what's the solution to solve these problems?**

Whenever the system is halted, you can run scripts in debuggee, and HyperDbg guarantees that the script will be executed while the debuggee \(both user-mode processes and kernel-mode\) kept halted.

The solution to this problem is to create the event before trying to debug the debuggee target process, for example, we execute the following command to get syscall 0x55 from the process with pid=0x490.

```c
HyperDbg> !syscall 55 pid 490
```

After that, we can see the event number, using events command.

Note that event number is `0` in this case.

```c
HyperDbg> events
0       (enabled)           !syscall 55 pid 490
```

Now, we disable the event using the following command.

```c
HyperDbg> events d 0
```

If we run the events command, you can see that the event `0` is on disabled state.

```c
HyperDbg> events
0       (disabled)          !syscall 55 pid 490
```

Now, we try to debug our target process and when we halt the debuggee in our target process, we can use the following command to \(from script-engine\) to re-enable the event.

Note that 0 is the event number.

```c
HyperDbg> ? EnableEvent(0);
```

After that, we receive the events relating to the event with event number `0`and we won't lose any events as the guest remained paused while the above expression command is executed.

At last, if we want to disable the event, we can use the following commad.

```c
HyperDbg> ? DisableEvent(0);
```

Using this way, we can solve the problem of losing some events in HyperDbg.


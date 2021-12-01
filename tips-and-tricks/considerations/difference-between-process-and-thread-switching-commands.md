---
description: When to use '.process', '.process2', '.thread', and '.thread2' commands
---

# Difference between process and thread switching commands

## Process switching commands

Switching to another process is useful when you want to debug a special user-mode process or a special kernel-mode driver, where you know what process you're trying to debug. The process switching is possible by using the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' and '[.process2](https://docs.hyperdbg.org/commands/meta-commands/.process)' commands.

### Implementation

The first command is based on intercepting the clock interrupt. Whenever the quantum slice of a thread is finished, the system timer throws a clock interrupt to the first core. After that, Windows notifies other cores through the IPI (Inter-processor interrupts), and all the cores are synced to change their running threads.

HyperDbg intercepts these interrupts (clock interrupts and IPI interrupts) for a limited amount of time. If the target process was running when the interrupt has arrived, HyperDbg halts the system again waits for further commands from the user. If the Windows never tries to run the target process, then HyperDbg keeps checking until the user halts the system again using CTRL+C or a breakpoint is triggered, or a break event is called. The same checking applies to all the threads and process checks.

The second implementation of this command ([.process2](https://docs.hyperdbg.org/commands/meta-commands/.process)) forces all the cores to cause VM-exit in the case of any changes to the **CR3** register. As you might know, each process has its unique memory layout, and memory layout is managed through the different **CR3** registers in x86 processors. Thus, whenever this register is changed, it means Windows tries to switch to a new process.

### Use cases

Generally, the first implementation ([.process](https://docs.hyperdbg.org/commands/meta-commands/.process)) is better and works most of the time. It's because when the check is performed, you hold the context (registers and memory layout) of the target thread (process). You can modify its context or step through the instructions. However, this method might not always work.

Sometimes, the thread might finish its works earlier than when the clock interrupt arrives (For example, by calling **WaitForSingleObject**). In these cases, Windows performs the context switch without using the clock interrupt as the thread is waiting for an object. Thus, we never get notified about the thread's execution, and HyperDbg won't halt again.

The second method is guaranteed to get the execution if Windows tries to run the target process. However, there is a caveat to this method. When this method halts the debugger, the context (registers) is in the kernel context switching routines of Windows; so, you don't have the context (registers) of the running thread when performing its normal user-mode or kernel-mode tasks.

The first method lets the process run for at least one quantum time slice, while the second method halts the system without giving a chance to the program to run.

Sometimes, you expect to get the execution when the process is running in the user-mode, but when you run the first command, you end up with the thread operating in the kernel-mode. The thread might be executing its life in the kernel-mode because of handling a system call or servicing an interrupt in the user-mode.

Most of the time, if you run the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' + the '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' commands several times, you can get the thread in the user-mode.

#### When to use '.process'?

* If you want to halt the debuggee when the process is in the user-mode.
* If you want to see and modify the actual context of the thread in the user-mode and the kernel-mode.
* If you want to step through the instructions in the target thread directly.

#### When to use '.process2'?

* If your process is context switched as a result of early waiting for an object.
* If you want to get notified about the process execution before it runs for a time slice.

## Thread switching commands

Switching to a new thread is useful when you have multiple threads in a process and want to debug a particular thread. In these cases, you should use thread switching to switch between different threads. The thread switching is possible by using the '[.thread](https://docs.hyperdbg.org/commands/meta-commands/.thread)' and '[.thread2](https://docs.hyperdbg.org/commands/meta-commands/.thread)' commands.

### Implementation

The implementation of the '[.thread](https://docs.hyperdbg.org/commands/meta-commands/.thread)' is exactly the same as the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command. Like the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)', it's based on intercepting the clock interrupts, and when the quantum slice of any thread in the system is finished, the system timer throws a clock interrupt to the first core. After that, Windows notifies other cores through the IPI (Inter-processor interrupts), and all the cores are synced to change their running threads.

HyperDbg gets the clock interrupts, and IPI interrupts for a limited amount of time. If the target thread was running when the interrupt has arrived, HyperDbg halts the system again waits for further commands from the user. If the Windows never tries to run the target thread, then HyperDbg keeps checking until the user halts the system again using CTRL+C or a breakpoint is triggered, or a break event is called.

The second implementation of this command ([.thread2](https://docs.hyperdbg.org/commands/meta-commands/.thread)) sets a break on access (write) to `gs:[188]` on all the cores to cause a debug breakpoint (#DB), and also a VM-exit is configured to happen when a debug breakpoint arrived (using the exception bitmap for the #DB). As a result, a VM-exit occurs in the case of any changes to the `gs:[188]`. Whenever Windows tries to write on this address, it means that a thread is changed as it stores the information about the threads on this address. This way, we will be notified that a thread is changed.

### Use cases

In almost all cases, you need to use the first implementation. Sometimes using the first method is not possible because the target thread is context switched without a clock interrupt, and you can't get the thread's execution.

When using the second method, the result of the '[.thread](https://docs.hyperdbg.org/commands/meta-commands/.thread)' or '[.prcoess](https://docs.hyperdbg.org/commands/meta-commands/.process)' showing the current thread or process might be wrong. It is because the target thread is about to be switched, and the switching is not finished yet. Also, the context of the debugger (registers) is based on kernel context switching routines, not based on the threads when executing in their normal execution.

#### When to use '.thread'?

* If you want to switch to a particular thread.
* If you have multiple threads and only want to debug one of them.
* If you're going to halt the debuggee when the thread is in the user-mode.
* If you want to see and modify the actual context of the thread in the user-mode and the kernel-mode.
* If you want to step through the instructions in the target thread directly.
* If a thread is halted and a deadlock happens, and you want to debug it

#### When to use '.thread2'?

* Only if you won't get a chance to get the thread's execution using the first method.

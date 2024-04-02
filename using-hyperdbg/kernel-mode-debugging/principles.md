---
description: Principles of designing a fast and reliable kernel-mode debugger
---

# Principles

The central part of the HyperDbg debugger is its kernel-mode debugger called **kHyperDbg**.&#x20;

It's almost two decades that kernel debuggers like WinDbg and GDB didn't change significantly. The HyperDbg contributors try to make a move toward advancing the kernel debugging and kernel debuggers.

Moreover, we think kernel mode is not the best playground for a kernel-debugger. A debugger who wants to debug the kernel mode should be more privileged in terms of hardware layers (rings).

In addition, a debugger is not allowed to manipulate the operating system's structures to make facilities for the debugging. Instead, we need a completely separate layer to monitor and change these structures without interfering with the operating system. The best place for reaching these goals is hypervisors.

The HyperDbg is a hypervisor-level debugger designed to pursue these needs.

### Design Goals

We aimed to make a powerful and fast debugger to bring new creative features into your debugging and analyzing journey.

The complexity of new binaries that run on kernel mode of operating systems and its importance on security and reliability of the systems needs a debugger that follows these needs with new features.

Likewise, the speed of the debugger combined with a powerful script engine makes you able to change the flow of the applications and even the operating system by using simple scripts.

### What makes it different?

Tens of distinctive characteristics and the fact that HyperDbg is tremendously faster in creating logs and checking for conditions based on its unique script engine design.

Furthermore, changing and monitoring the memory brings thousands of features that a researcher or a programmer can use to analyze and research binary modules.

Similarly, features like stepping from user mode to kernel mode or kernel mode to user mode are only available in the HyperDbg debugger. These are practical features and approaches that ease the binary analysis procedure.

HyperDbg is transparent by its nature. However, we tried to make it even more transparent by implementing the [Transparent Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#transparent-mode). Although, it won't guarantee 100% of transparency. Still, it makes it substantially harder for the anti-debugging and anti-hypervisor methods to detect the debugger's presence.

### What to know?

The kernel debugger works on both VMI Mode and Debugger Mode.

The difference between running in the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode) and the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode) is that you can't halt (pause) the debugger to step through the instruction or use the "[break](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#break)" actions in the VMI Mode.

In the Debugger Mode, HyperDbg sends the buffer and messages immediately, but these messages will be buffered first and delivered at a suitable time in the VMI Mode.

Unlike WinDbg, some of the commands are implemented from user mode and will continue the debuggee for some time, and you'll lose the contexts (registers, memory). You need to consider that as described in the command's documentation.

You need to follow some safety rules in accessing memory and avoid "[unsafe behavior](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)" in using HyperDbg.

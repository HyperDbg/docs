---
description: Principles of designing a standalone user-mode debugger
---

# Principles

HyperDbg is a unique debugger. The intention of creating this tool was, of course, not just debugging. Regardless of debugging components, it also aims to bring a framework to use the innovative feature of processors for debugging and analyzing binaries. Here we discuss some of the principles of the user debugger.

{% hint style="danger" %}
In contrast with the kernel debugger, the user debugger is still very basic and needs a lot of tests and improvements. We **highly recommend** not to run the user debugger in your bare metal system. Instead, run it on a supported virtual machine to won't end up with a Blue Screen of Death (BSOD) in your primary device. Please keep reporting the issues to improve the user debugger.
{% endhint %}

### Design Goals

In the user debugger, we aimed to make a lightweight debugger that integrates the different subsystems of the HyperDbg into a fast, reliable user-mode debugging tool.

There is no need to use kernel debugging facilities to debug a user-mode application when the entire debugging module runs on user mode. The user doesn't care what is running on the kernel side and just needs to debug the user mode side of the binary. That's why HyperDbg is armed with a user debugger.

Although, the user debugger is primarily designed to use in the user-mode application. Still, it can change the kernel mode and monitor kernel events.

### What makes it different?

&#x20;HyperDbg didn't use any debugging API in the debugging process. Everything is handled at the hypervisor level. This brings a huge benefit as even Windows is unaware that the process is under debugging.

Moreover, all of the magical features of HyperDbg are integrated into the user debugger. You can debug a user-mode process with the capabilities of HyperDbg debugger.

Additionally, you can simultaneously debug multiple processes with kernel-mode debugging facilities on a single debugging session.

### What to know?

The user debugger works in the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode).

Whenever you start a new process or attach to the target process, HyperDbg starts the "**Thread Interception**" phase.

During this phase, HyperDbg will intercept every thread that tries to run an instruction from the user-mode (Not kernel mode). Thus, some threads might remain in kernel mode without returning to the user mode and be left unintercepted.

The "**Thread Interception**" phase continues as long as you run step-in or a step-over instruction or ultimately continue the process.

After that, the new threads or those threads that return from the kernel won't be caught by HyperDbg, and you need to pause the process again to intercept these threads.

It's recommended to keep interacting with the target process to make as many threads running to intercept them in the user debugger.

The user debugger can only step through the user-mode instructions. You can't step through the kernel-mode instructions.

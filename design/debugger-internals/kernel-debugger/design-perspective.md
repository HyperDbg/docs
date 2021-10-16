---
description: Description of high-level design of kernel debugger
---

# Design Prospective

The following diagram illustrates how HyperDbg works on its kernel debugging mechanism.

![](../../../.gitbook/assets/debugger-prospective.png)

Based on the design of HyperDbg, all the cores are halted and spinning on ring -1 \(hypervisor\).

The current operating code is listening for new commands from the debugger on a polling mode serial. 

There are two scenarios in which the kernel debugger is paused.

1. The user requests a pause \(for example, by pressing CTRL+C\), then it sends a packet to pause the debugger, and the debuggee starts processing the packet from user-mode and invokes an IOCTL, and that IOCTL executes a VMCALL and goes from the kernel-mode to the vmx-root.
2. A breakpoint is triggered either by a break request from an event or the script engine. There are two possible scenarios, first, if the user is in kernel-mode, then a VMCALL happens, and if the user is already in vmx-root mode, we should notify all the other cores.

In order to notify all the other cores, we send NMIs to all the cores using XAPIC or X2APIC. All the cores are configured to cause vm-exit in the case of NMIs \(PIN-Based VM-Exec controls are set to 1\).

All the cores are in vmx-root mode are spinning and waiting for a new command from the debugger.


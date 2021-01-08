---
description: Frequently Asked Questions (FAQ)
---

# FAQ

## What is HyperDbg?

HyperDbg is an open-source, hypervisor-assisted [debugger](https://en.wikipedia.org/wiki/Debugger). You can use HyperDbg to debug both user-mode and kernel-mode applications.

## Who uses HyperDbg?

Programmers, security researchers, malware analyzers, fuzzer programmers.

## Why do we need HyperDbg?

Because HyperDbg gives you unique abilities to use modern processor features that will assist you in your reverse engineering journey.

You can see a list of these features [here](https://github.com/HyperDbg/HyperDbg#unique-features).

## What makes HyperDbg different from classic debuggers?

HyperDbg has a unique architecture. The principles of designing HyperDbg are making an OS-independent debugger and leveraging modern processor features to bring new reverse engineering methods; thus, the features you see in HyperDbg is not available in other debuggers.

## What's the difference between HyperDbg and Windbg?

HyperDbg has a completely different architecture. Windbg operates on ring 0 \(kernel\) while HyperDbg is running on ring -1 \(hypervisor\); thus, HyperDbg provides unique features that are not available on Windbg \(OS-Level\). 

Besides that, HyperDbg is not just a simple debugger. It comes up with modern reverse engineering methods by using vt-x and other modern processor facilities to ease the reverse engineering, analyzing, and fuzzing.

## Is it only for a special processor? Or can I run it on AMD or ARM processors?

The current version of HyperDbg only supports Intel x64 processors. You cannot run it on an AMD processor or an ARM processor, but future versions will support other processors.

## What generation of Intel Processors supports HyperDbg?

Your processor should support Intel Extended Page Table \(A.K.A. EPT\), which is introduced on Nehalem Microarchitecture, but some functionalities only work on Intel's 4th or later generation; so, the previous processors might have undefined behaviors with some of the functionalities of HyperDbg.  It's recommended to use a **Skylake** \(6th generation\) processor or newer processors.

## Can I use it on Linux or macOS?

No, the current version is only limited to Windows 10; however, one of our top priorities is to port HyperDbg on Linux, but currently, it's only usable on Windows.

## Should I have a separate machine to use HyperDbg?

Of course not! The only problem with not having a separate machine is that you can only operate on [VMI mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#vmi-mode), and you can't pause \(halt\) the system with breakpoint or for stepping. You can also use VMware Workstation to debug in [debugger mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode) with all of the features, including stepping and pausing the debuggee.

## Can I use it on a nested-virtualization environment \(VMware, VirtualBox, Hyper-V\)?

The current versions of HyperDbg are only tested on VMware Workstation \(Not VMware player\), but in the future versions, we will support all the virtualization platforms with nested-virtualization. 

## How can I start reading about HyperDbg internals? How does it work? Can I contribute to HyperDbg?

The source for reading about Hypervisors and HyperDbg internals is [Hypervisor From Scratch](https://rayanfam.com/tutorials/) tutorials.

For contribution, you can follow the [contribution guide](https://github.com/HyperDbg/HyperDbg/blob/master/CONTRIBUTING.md).

You can visit [here ](https://docs.hyperdbg.com/design)to know more about HyperDbg [design ](https://docs.hyperdbg.com/design)internals.


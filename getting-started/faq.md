---
description: Frequently Asked Questions (FAQ)
---

# FAQ

## What is HyperDbg?

HyperDbg is an open-source, hypervisor-assisted [debugger](https://en.wikipedia.org/wiki/Debugger). You can use HyperDbg to debug both user-mode and kernel-mode applications.

## Who uses HyperDbg?

Programmers, Security Researchers, Binary Analyzers.

## Why do we need HyperDbg?

Because HyperDbg gives you unique abilities to use modern process features which will asisst       you 

## What makes HyperDbg different from classic debuggers?

## What's the difference between HyperDbg and Windbg?

## Is it only for a special processor? or can I run it on AMD or ARM processors?

Current version of HyperDbg only supports Intel processor. You cannot run it on an AMD processor or an ARM processors.

## What generation of Intel Processors supports HyperDbg?

Your processor should support Intel Extended Page Table \(A.K.A. EPT\) which is introduced on Nehalem Microarchitecture but some of the functionalities only works on 4th or later generation of Intel processor. so the previous processors might have undefined behaviors with HyperDbg. For best experience, it's recommended to use an Skylake \(6th gen\) processor or newer processors.

## Can I use it on Linux or macOS?

No, the current version is only limited to Windows 10; however, one of our top priorities is to port HyperDbg on Linux but currently it's only usable on Windows.

## Should I have a separate machine to use HyperDbg?

You can 

## Can I use it on a nested-virtualization environment \(VMware, VirtualBox, Hyper-V\)?

Current version of HyperDbg is only tested on VMware Workstation \(Not VMware player\) but in the future versions we will support all the virtualization platforms with nested-virtualization. 

## 

## How can I start reading? How does it work? Can I contribute to HyperDbg?

The source for reading about Hypervisors and HyperDbg internals is [Hypervisor From Scratch](https://rayanfam.com/tutorials/) tutorials.

For contribution, you can follow the [contribution guide](https://github.com/HyperDbg/HyperDbg/blob/master/CONTRIBUTING.md).




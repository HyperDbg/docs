# Table of contents

* [HyperDbg](README.md)

## Getting Started

* [Build & Install](getting-started/build-and-install.md)
* [Attach to HyperDbg](getting-started/attach-to-hyperdbg/README.md)
  * [Attach to remote machine](getting-started/attach-to-hyperdbg/attach-to-remote-machine.md)
  * [Attach to local machine](getting-started/attach-to-hyperdbg/attach-to-local-machine.md)
* [Quick Start](getting-started/quick-start.md)

## Using HyperDbg

* [Prerequisites](using-hyperdbg/prerequisites/README.md)
  * [How to create a condition?](using-hyperdbg/prerequisites/how-to-create-a-condition.md)
  * [How to create an action?](using-hyperdbg/prerequisites/how-to-create-an-action.md)
* [Examples](using-hyperdbg/examples.md)

## Commands

* [Debugging Commands](commands/debugging-commands/README.md)
  * [load \(load the kernel modules\)](commands/debugging-commands/load.md)
  * [unload \(unload the kernel modules\)](commands/debugging-commands/unload.md)
  * [lm \(view loaded modules\)](commands/debugging-commands/lm.md)
  * [db, dc, dd, dq \(read virtual memory\)](commands/debugging-commands/d.md)
  * [u \(disassemble virtual address\)](commands/debugging-commands/u.md)
  * [cpu \(check cpu supported technologies\)](commands/debugging-commands/cpu.md)
  * [rdmsr \(read model-specific register\)](commands/debugging-commands/rdmsr.md)
  * [wrmsr \(write model-specific register\)](commands/debugging-commands/wrmsr.md)
  * [exit \(exit from the debugger\)](commands/debugging-commands/exit.md)
* [Meta Commands](commands/meta-commands/README.md)
  * [.connect \(connect to a session\)](commands/meta-commands/.connect.md)
  * [.disconnect \(disconnect from a session\)](commands/meta-commands/.disconnect.md)
  * [.formats \(show number formats\)](commands/meta-commands/.formats.md)
  * [.script \(run batch script of HyperDbg commands\)](commands/meta-commands/.script-run-batch-script-of-hyperdbg-commands.md)
  * [.cls \(clear the screen\)](commands/meta-commands/.cls.md)
* [Extension Commands](commands/extension-commands/README.md)
  * [!pte \(display page-level address and entries\)](commands/extension-commands/pte.md)
  * [!db, !dc, !dd, !dq \(read physical memory\)](commands/extension-commands/d.md)
  * [!u \(disassemble physical address\)](commands/extension-commands/u.md)
  * [!epthook \(hidden hook with EPT - stealth breakpoints\)](commands/extension-commands/epthook.md)
  * [!epthook2 \(hidden hook with EPT - detours\)](commands/extension-commands/epthook2.md)
  * [!monitor \(monitor read/write to a page\)](commands/extension-commands/monitor.md)
  * [!syscall \(hook system-calls\)](commands/extension-commands/syscall.md)
  * [!sysret \(hook SYSRET instruction execution\)](commands/extension-commands/sysret.md)
  * [!cpuid \(hook CPUID instruction execution\)](commands/extension-commands/cpuid.md)
  * [!msrread \(hook RDMSR instruction execution\)](commands/extension-commands/msrread.md)
  * [!msrwrite \(hook WRMSR instruction execution\)](commands/extension-commands/msrwrite.md)
  * [!tsc \(hook RDTSC/RDTSCP instruction execution\)](commands/extension-commands/tsc.md)
  * [!pmc \(hook RDPMC instruction execution\)](commands/extension-commands/pmc.md)
  * [!vmcall \(hook hypercalls\)](commands/extension-commands/vmcall.md)
  * [!exception \(hook first 32 entries of IDT\)](commands/extension-commands/exception.md)
  * [!interrupt \(hook external device interrupts\)](commands/extension-commands/interrupt.md)
  * [!dr \(hook access to debug registers\)](commands/extension-commands/dr.md)
  * [!ioin \(hook IN instruction execution\)](commands/extension-commands/ioin.md)
  * [!ioout \(hook OUT instruction execution\)](commands/extension-commands/ioout.md)

## Tips & Tricks

* [Considerations](tips-and-tricks/considerations/README.md)
  * [Basic Concepts in Intel VT-x](tips-and-tricks/considerations/basic-concepts-in-intel-vt-x.md)
  * [Vmx root-mode vs Vmx non-root mode](tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode.md)
  * [The "unsafe" behavior](tips-and-tricks/considerations/the-unsafe-behavior.md)
* [Nested-Virtualization Environments](tips-and-tricks/nested-virtualization-environments/README.md)
  * [Run HyperDbg on VMware](tips-and-tricks/nested-virtualization-environments/run-hyperdbg-on-vmware.md)
  * [Run HyperDbg on Hyper-V](tips-and-tricks/nested-virtualization-environments/run-hyperdbg-on-hyper-v.md)
  * [Supporting VMware/Hyper-V](tips-and-tricks/nested-virtualization-environments/supporting-vmware-hyper-v.md)
  * [VMware backdoor I/O ports](tips-and-tricks/nested-virtualization-environments/vmware-backdoor-io-ports.md)

## Style Guide

* [Coding Style](style-guide/coding-style.md)
* [Command Style](style-guide/command-style.md)
* [Doxygen Style](style-guide/doxygen-style.md)
* [Logo & Artworks](style-guide/logo.md)

## Design

* [Features](design/features/README.md)
  * [Design of !epthook2](design/features/design-of-epthook2.md)
  * [Design of !monitor](design/features/design-of-monitor.md)
* [Debugger Internals](design/debugger-internals/README.md)
  * [Events](design/debugger-internals/events.md)
  * [Conditions](design/debugger-internals/conditions.md)
  * [Actions](design/debugger-internals/actions.md)
  * [IOCTL Requests for Events](design/debugger-internals/ioctl-requests-for-events.md)
  * [VMX Root-mode Compatible Message Tracing](design/debugger-internals/vmx-root-mode-compatible-message-tracing.md)
  * [Control over NMIs](design/debugger-internals/control-over-nmis.md)

## Links

* [Twitter](https://twitter.com/HyperDbg)
* [Releases](https://github.com/HyperDbg/HyperDbg/releases)
* [Doxygen](https://doxygen.hyperdbg.com/)
* [Contribution](https://github.com/HyperDbg/HyperDbg/blob/master/CONTRIBUTING.md)
* [Blog](https://rayanfam.com/)


# Table of contents

* [HyperDbg Debugger](README.md)

## Getting Started

* [Build & Install](getting-started/build-and-install.md)
* [Attach to HyperDbg](getting-started/attach-to-hyperdbg/README.md)
  * [Attach to remote machine](getting-started/attach-to-hyperdbg/attach-to-remote-machine.md)
  * [Attach to local machine](getting-started/attach-to-hyperdbg/attach-to-local-machine.md)

## Using HyperDbg

* [Prerequisites](using-hyperdbg/prerequisites/README.md)
  * [How to create a condition?](using-hyperdbg/prerequisites/how-to-create-a-condition.md)
  * [How to create an action?](using-hyperdbg/prerequisites/how-to-create-an-action.md)
* [Examples](using-hyperdbg/examples.md)
* [Features](using-hyperdbg/features/README.md)
  * [Hidden Hooks](using-hyperdbg/features/hidden-hooks/README.md)
    * [Execution Hook](using-hyperdbg/features/hidden-hooks/execution-hook.md)
    * [R/W Hook](using-hyperdbg/features/hidden-hooks/r-w-hook.md)
  * [Syscall Hook](using-hyperdbg/features/syscall-hook.md)

## Tips & Tricks

* [Considerations](tips-and-tricks/considerations/README.md)
  * [The "unsafe" behavior](tips-and-tricks/considerations/the-unsafe-behavior.md)

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
  * [.cls \(clear the screen\)](commands/meta-commands/.cls.md)
* [Extension Commands](commands/extension-commands/README.md)
  * [!pte \(display page-level address and entries\)](commands/extension-commands/pte.md)
  * [!db, !dc, !dd, !dq \(read physical memory\)](commands/extension-commands/d.md)
  * [!u \(disassemble physical address\)](commands/extension-commands/u.md)
  * [!hiddenhook \(hidden hook with EPT\)](commands/extension-commands/hiddenhook.md)
  * [!monitor \(monitor read/write to a page\)](commands/extension-commands/monitor.md)
  * [!syscallhook \(hook system-calls\)](commands/extension-commands/syscallhook.md)

## Style Guide

* [Coding Style](style-guide/coding-style.md)
* [Command Style](style-guide/command-style.md)
* [Doxygen Style](style-guide/doxygen-style.md)
* [Logo & Artworks](style-guide/logo.md)

## How does it work?

* [Features Design](how-does-it-work/features-design.md)
* [Debugger Internals](how-does-it-work/debugger-internals/README.md)
  * [Events](how-does-it-work/debugger-internals/events.md)
  * [Conditions](how-does-it-work/debugger-internals/conditions.md)
  * [Actions](how-does-it-work/debugger-internals/actions.md)

## Links

* [Twitter](https://twitter.com/HyperDbg)
* [Releases](https://github.com/SinaKarvandi/HyperDbg/releases)
* [Doxygen](https://doxygen.hyperdbg.com/doxygen)
* [Contribution](https://github.com/HyperDbg/HyperDbg/blob/master/CONTRIBUTING.md)
* [Blog](https://rayanfam.com/)


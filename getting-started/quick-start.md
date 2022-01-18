---
description: A brief overview of how to start with HyperDbg
---

# Quick Start

## Starting with HyperDbg

First of all, you should know about different [**Operation Modes**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes) and [**Prerequisites** ](https://docs.hyperdbg.org/using-hyperdbg/prerequisites)in HyperDbg.

* [**VMI Mode**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode)
* [**Debugger Mode**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)
* [**Transparent Mode**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#transparent-mode)

If you want to build HyperDbg, follow the steps in the links below; you can also download compiled binaries instead of building HyperDbg.

* [**Build & Install**](https://docs.hyperdbg.org/getting-started/build-and-install)
* [**Download Releases**](https://github.com/HyperDbg/HyperDbg/releases)

After that, you need to attach to an instance of HyperDbg.

* [**Local Debugging (VMI Mode)**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/attach-to-local-machine)
* [**Debug a Physical Machine (Debugger Mode)**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/debug#physical-machine)
* [**Debug a VMware Workstation Guest (Debugger Mode)**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/debug#vmware-workstation)
* [**Connect to a Remote Machine (VMI Mode)**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/debug#connect-to-debuggee-vmi-mode)
* ****[**Start a new process**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/start-process)****
* ****[**Attach to a running process**](https://docs.hyperdbg.org/getting-started/attach-to-hyperdbg/attach-process)****

Finally, you can see many examples provided in the following link to use HyperDbg.

**User-mode Debugging examples:**

{% content-ref url="../using-hyperdbg/user-mode-debugging/examples/" %}
[examples](../using-hyperdbg/user-mode-debugging/examples/)
{% endcontent-ref %}

**Kernel-mode Debugging examples:**

{% content-ref url="../using-hyperdbg/kernel-mode-debugging/examples/" %}
[examples](../using-hyperdbg/kernel-mode-debugging/examples/)
{% endcontent-ref %}

**Script engine examples:**

{% embed url="https://docs.hyperdbg.org/commands/scripting-language/examples" %}

_**Done!** You successfully used HyperDbg Debugger._

## List of HyperDbg Commands

* [**Debugging Commands**](https://docs.hyperdbg.org/commands/debugging-commands)
* [**Meta Commands**](https://docs.hyperdbg.org/commands/meta-commands)
* [**Extension Commands**](https://docs.hyperdbg.org/commands/extension-commands)

## Scripting Language

* [**Assumptions & Evaluations**](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations)
* [**Conditionals & Loops**](https://docs.hyperdbg.org/commands/scripting-language/conditionals-and-loops)
* [**Variables & Assignments**](https://docs.hyperdbg.org/commands/scripting-language/variables-and-assignments)
* [**Script Functions**](https://docs.hyperdbg.org/commands/scripting-language/functions)
* [**Script Examples**](https://docs.hyperdbg.org/commands/scripting-language/examples)
* [**List of Script Functions**](https://docs.hyperdbg.org/commands/scripting-language/functions)

## **More advanced topics...**

If you want to see more advanced topics about HyperDbg.

* [**How to create an action?**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action)
* [**How to create a condition?**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition)

## Things to consider when using HyperDbg

* [**Basic Concepts in Intel VT-x**](https://docs.hyperdbg.org/tips-and-tricks/considerations/basic-concepts-in-intel-vt-x)
* [**VMX root-mode vs VMX non-root mode**](https://docs.hyperdbg.org/tips-and-tricks/considerations/vmx-root-mode-vs-vmx-non-root-mode)
* [**The "unsafe" behavior**](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)
* [**Script engine in VMX non-root mode**](https://docs.hyperdbg.org/tips-and-tricks/considerations/script-engine-in-vmx-non-root-mode)
* [**Difference between process and thread switching commands**](https://docs.hyperdbg.org/tips-and-tricks/considerations/difference-between-process-and-thread-switching-commands)****
* [**Transparent Mode**](https://docs.hyperdbg.org/tips-and-tricks/considerations/transparent-mode)

## Miscellaneous topics...

* [**Event Forwarding Mechanism**](https://docs.hyperdbg.org/tips-and-tricks/misc/event-forwarding)
* [**Message Overflow**](https://docs.hyperdbg.org/tips-and-tricks/misc/message-overflow)
* ****[**Switch to New Process Layout**](https://docs.hyperdbg.org/tips-and-tricks/misc/switch-to-new-process-layout)****
* ****[**Customize Build**](https://docs.hyperdbg.org/tips-and-tricks/misc/customize-build)
* [**Enable and Disable Events in Debugger Mode**](https://docs.hyperdbg.org/tips-and-tricks/misc/enable-and-disable-events-in-debugger-mode)

## If you want to participate in HyperDbg

* [**Coding Style**](https://docs.hyperdbg.org/contribution/style-guide/coding-style)
* [**Command Style**](https://docs.hyperdbg.org/contribution/style-guide/command-style)
* [**Doxygen Style**](https://docs.hyperdbg.org/contribution/style-guide/doxygen-style)
* [**Logo & Artworks**](https://docs.hyperdbg.org/style-guide/logo)

You can also follow the [**contribution guide**](https://github.com/HyperDbg/HyperDbg/blob/master/CONTRIBUTING.md).

## **Any other questions**

If you have any other questions, then it's probably addressed in the [**FAQ**](https://docs.hyperdbg.org/getting-started/faq) section. If your question remained unanswered or your problem is unsolved, then you can create an [**issue**](https://github.com/HyperDbg/HyperDbg/issues).

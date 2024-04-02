---
description: >-
  Description of considerations and limitations in using script engine in vmx
  non-root mode
---

# Script engine in VMX non-root mode

The current version of HyperDbg's script engine is designed to work on vmx root-mode.

Some commands like [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2) are not triggered on vmx root-mode, and these commands operate on vmx non-root mode. Even though it's possible, but from a design perspective, we prefer to keep these commands in vmx non-root mode and won't trigger them in vmx root-mode.

This way, these commands are faster as we won't have a transition (**vm-exit** and **vm-entry**).

On the other hand, the script engine is a crucial feature for the debugger, and all of the commands are supposed to support it.

These limitations are primarily originated from the fact that we access registers like **@rip**, **@rsp**, **@rflags**, **@cs**, **@ds**, and other segment registers from the vmx root-mode using **VMREAD** and **VMWRITE** instructions.

**VMREAD** and **VMWRITE** are not supposed to work on vmx non-root mode; thus, if you use a script like:

```
printf("RIP: %llx\n", @rip);
```

You'll end up with BSODs. Still, you can safely use general-purpose registers, conditional statements, etc.

For instance, the following script is okay.

```
printf("RAX: %llx\n", @rax);
```

Please consider these assumptions when you're using the script engine with vmx non-root commands.

In future versions, these limitations will be fixed, and you can use the script engine with vmx non-root commands as well as vmx-root mode commands.

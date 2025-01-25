---
description: Description of the '!hide' command in HyperDbg.
---

# !hide (enable transparent-mode)

### Command

> !hide

### Syntax

> !hide

### Description

Enables the [transparent mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#transparent-mode) of **HyperDbg** for anti-debugging and anti-hypervisor methods.

### Parameters

None

### Examples

If you want to hide **HyperDbg** and enter the [transparent mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#transparent-mode).

```
HyperDbg> !hide
```

### SDK

To enable the transparent mode, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_enable_transparent_mode();
```

### Remarks

This command will not guarantee to provide **100%** transparency, especially in nested-virtualization environments.

{% hint style="warning" %}
Due to the transparent mode, some commands may not work as expected or may result in undefined behavior.
{% endhint %}

**HyperDbg** will protect you from some anti-hypervisor methods; however, there are other traces for anti-VMware, anti-VirtualBox, etc. methods and these methods are still problematic because, **HyperDbg** tries to hide itself from anti-debugging and anti-hypervisor methods and it won't hide VMware, VirtualBox, etc. thus, you need to run this command in a physical-machine (not in a nested-virtualization environment); otherwise you should find other traces for virtual machine software and solve those traces by yourself (e.g., hooking anti-VMware APIs and Queries).

This command is frequently updated and new methods will be mitigated in the transparent mode.

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[!unhide (disable transparent-mode)](https://docs.hyperdbg.org/commands/extension-commands/unhide)

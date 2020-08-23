---
description: Different Modes of Operation in HyperDbg
---

# Operation Modes

In **HyperDbg**, we operate in **three** different operation modes.

The first and the basic mode of operation for **local** and **remote debugging** is **Survey Mode**. The second mode is **Debugger Mode** and the third mode is **Transparent Mode**.

## Survey Mode

If you want to use **HyperDbg**, then you should operate at least in **Survey Mode**. In both local debugging and remote debugging, **Survey Mode** should be enabled.

In this mode, you can use all of the **HyperDbg** features, except break to the debugger and step instructions in **kernel mode**. Still, you can break to the debugger and step user-mode instruction without any limitation.

Based on **HyperDbg** [actions](https://docs.hyperdbg.com/using-hyperdbg/prerequisites), only **break to the debugger** for **kernel-mode** \(not user mode\) is not available in this mode, and you can use log the states and run custom codes in both user mode and kernel mode. 

You can use this mode in both **local debugging** and **remote debugging**.

## Debugger Mode

If you want to use **HyperDbg** for breaking the kernel and halt the system in order to step in and step out through the kernel instructions, then you should use this mode of operation.

It is obvious that you can't use this mode of operation for **local debugging** and you provide a system with a **serial cable** or **virtual serial** device to enable it.

In order to enable this mode, you should also connect from debuggee to debugger by **Survey Mode**; it is because **HyperDbg** transfers its data over **TCP** won't transfer the logs and data over **serial** because it is slow.

## Transparent Mode

Transparent Mode is an entirely different concept.

If you enable this mode, then **HyperDbg** tries to make itself transparent from anti-debugging and anti-hypervisor methods and also not to reveal the presence of hypervisor on timing and microarchitectural attacks; however, it won't guarantee 100% of transparency, but it makes it substantially harder for the anti-debugging methods. You can read more about the design of Transparent Mode.

To enable this mode, you should use '[!measure](https://docs.hyperdbg.com/commands/extension-commands/measure)' and '[!hide](https://docs.hyperdbg.com/commands/extension-commands/hide)' commands.

You can use **Transparent Mode** in both **Survey Mode** and **Debugger Mode**.




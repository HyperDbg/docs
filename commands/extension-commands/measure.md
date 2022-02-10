---
description: Description of '!measure' command in HyperDbg.
---

# !measure (measuring and providing details for transparent-mode)

### Command

> !measure

### Syntax

> !measure \[default]

### Description

Measures and provides the details for the transparent-mode of **HyperDbg** for defeating anti-debugging and anti-hypervisor methods.

This command should be run before you '[load](https://docs.hyperdbg.org/commands/debugging-commands/load)' the debugger, and after that, you can use the '[!hide](https://docs.hyperdbg.org/commands/extension-commands/hide)' command.

### Parameters

**\[default] (optional)**

If you specify '**default**', then **HyperDbg** uses the hardcoded measurements from a not-running hypervisor machine; however, it's not recommended. See the **Remarks** for more information.

### Examples

The following command measures and provides statistics for transparent-mode based on your machine.

```
HyperDbg> !measure
```

The following command uses the hardcoded results and statistics for a not-running hypervisor machine.

```
HyperDbg> !measure default
```

### IOCTL

None

### Remarks

If you are running on a nested-virtualization environment, then the result of the measurements will not provide transparency for you. Instead, you can use the following command :

```
HyperDbg> !measure default
```

The above command uses hardcoded details from a not-running hypervisor, and this way, you can provide transparency for vm-exit. However, it does not belong to your machine, so it's highly recommended to let **HyperDbg** measure and provide your own machine's details.

{% hint style="info" %}
**IMPORTANT NOTE: USING DEFAULT MEASUREMENTS WON'T MAKE YOU 100% TRANSPARENT AS EACH VIRTUAL MACHINE SOFTWARE HAS ITS OWN TRACES, SO YOUR TARGET MIGHT CHECK FOR OTHER POSSIBLE TRACES AND FIGURE OUT THE PRESENCE OF THE VIRTUAL MACHINE.**
{% endhint %}

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[!hide (enable transparent-mode)](https://docs.hyperdbg.org/commands/extension-commands/hide)

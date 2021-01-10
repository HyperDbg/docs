---
description: Description of '!measure' command in HyperDbg.
---

# !measure \(measuring and providing details for transparent-mode\)

### Command

> !measure

### Syntax

> !measure \[default\]

### Description

Measuring and providing the details for the transparent-mode of **HyperDbg** for defeating anti-debugging and anti-hypervisor methods.

This command should be run before you '[load](https://docs.hyperdbg.com/commands/debugging-commands/load)' the debugger and after that, you can use '[!hide](https://docs.hyperdbg.com/commands/extension-commands/hide)' command.

### Parameters

**\[default\]**

          If you specify '**default**' then **HyperDbg** uses the hardcoded measurements from a not-running hypervisor machine; however, it's not recommended, see the **Remarks** for more information.

### Examples

The following command measures and provides statistics for transparent-mode based on your machine.

```text
HyperDbg> !measure
```

The following command uses the hardcoded results and statistics for a not-running hypervisor machine.

```text
HyperDbg> !measure default
```

### IOCTL

None

### **Remarks**

If you are running on a nested-virtualization environment, then the result of the measurements will not provide transparency for you, instead, you can use the following command :

```text
HyperDbg> !measure default
```

The above command uses hardcoded details from a not-running hypervisor and this way you can provide transparency for vm-exit. However, it does not belongs to your machine so it's highly recommended to let **HyperDbg** measure and provide the details for your own machine.

{% hint style="info" %}
**IMPORTANT NOTE: USING DEFAULT MEASUREMENTS WON'T MAKE YOU 100% TRANSPARENT AS EACH VIRTUAL MACHINE SOFTWARE HAS IT'S OWN TRACES, SO YOUR TARGET MIGHT CHECK FOR OTHER POSSIBLE TRACES AND FIGURE OUT THE PRESENCE OF THE VIRTUAL MACHINE.**
{% endhint %}

### Requirements

None

### Related

[!hide \(enable transparent-mode\)](https://docs.hyperdbg.com/commands/extension-commands/hide)


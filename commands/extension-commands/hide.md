---
description: Description of '!hide' command in HyperDbg.
---

# !hide \(enable transparent mode\)

### Command

> !hide

### Syntax

> !hide

### Description

Enable transparent-mode of HyperDbg for anti-debugging and anti-hypervisor methods.

### Parameters

None

### Examples

```text
HyperDbg >!hide
```

### IOCTL

None

### **Remarks**

This command will not guarantee to provide 100% transparency.

**HyperDbg** will not protect you from anti-WMware, anti-VirtualBox, etc. methods, instead, it tries to hide itself from anti-debugging and anti-hypervisor methods thus you need to run this command in a physical-machine \(not nested-virtualization environments\).

### Requirements

None

### Related

[.connect \(connect to a sessio](https://docs.hyperdbg.com/commands/meta-commands/.connect)


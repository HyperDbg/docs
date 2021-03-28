---
description: Description of 'lm' command in HyperDbg.
---

# lm \(view loaded modules\)

### Command

> lm

### Syntax

> lm \[module name\]
>
> lm

### Description

Shows the loaded modules' base address, size, name, full path.

### Parameters

**\[module name\] \(optional\)**

          The name or a part of the name that will be searched through all the modules and only those which match will be showed.

### Examples

The following command shows all the modules in the system.

```cpp
HyperDbg> lm
start                   size    name                            path

fffff801`1b800000       1046000 ntoskrnl.exe                    \SystemRoot\system32\ntoskrnl.exe
fffff801`195d0000       6000    hal.dll                         \SystemRoot\system32\hal.dll
fffff801`195e0000       b000    kd.dll                          \SystemRoot\system32\kd.dll
fffff801`19230000       39d000  mcupdate_GenuineIntel.dll       \SystemRoot\system32\mcupdate_GenuineIntel.dll
fffff801`1ec00000       69000   CLFS.SYS                        \SystemRoot\System32\drivers\CLFS.SYS
fffff801`195f0000       27000   tm.sys                          \SystemRoot\System32\drivers\tm.sys
fffff801`1ec70000       1a000   PSHED.dll                       \SystemRoot\system32\PSHED.dll
fffff801`19620000       b000    BOOTVID.dll                     \SystemRoot\system32\BOOTVID.dll
fffff801`1edb0000       6f000   FLTMGR.SYS                      \SystemRoot\System32\drivers\FLTMGR.SYS
fffff801`1ee50000       62000   msrpc.sys                       \SystemRoot\System32\drivers\msrpc.sys
fffff801`1ee20000       29000   ksecdd.sys                      \SystemRoot\System32\drivers\ksecdd.sys
fffff801`1ec90000       113000  clipsp.sys                      \SystemRoot\System32\drivers\clipsp.sys
fffff801`1eec0000       e000    cmimcext.sys                    \SystemRoot\System32\drivers\cmimcext.sys
fffff801`1eed0000       11000   werkernel.sys                   \SystemRoot\System32\drivers\werker
...
```

The following example shows the modules that contain "**nt"** in their path or name.

```c
HyperDbg> lm nt
start                   size    name                            path

fffff801`1b800000       1046000 ntoskrnl.exe                    \SystemRoot\system32\ntoskrnl.exe
fffff801`19230000       39d000  mcupdate_GenuineIntel.dll       \SystemRoot\system32\mcupdate_GenuineIntel.dll
fffff801`1eef0000       c000    ntosext.sys                     \SystemRoot\System32\drivers\ntosext.sys
fffff801`1f260000       1a000   SgrmAgent.sys                   \SystemRoot\system32\drivers\SgrmAgent.sys
fffff801`1f370000       6b000   intelpep.sys                    \SystemRoot\System32\drivers\intelpep.sys
fffff801`1f400000       b000    IntelTA.sys                     \SystemRoot\System32\drivers\IntelTA.sys
fffff801`1f7a0000       1e000   mountmgr.sys                    \SystemRoot\System32\drivers\mountmgr.sys
fffff801`20160000       7f000   fwpkclnt.sys                    \SystemRoot\System32\drivers\fwpkclnt.sys
fffff801`23820000       40000   intelppm.sys                    \SystemRoot\System32\drivers\intelppm.sys
fffff801`27180000       b000    KeyAgent.sys                    \??\C:\Windows\system32\drivers\KeyAgent.sys
```

### IOCTL

This function work by calling **NtQuerySystemInformation** and does not gets the address from the kernel, so it doesn't have any IOCTL.

### **Remarks**

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

### Requirements

None

### Related

None


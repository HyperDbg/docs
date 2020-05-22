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

Shows the loaded modules' base address, size, name , full path.

### Parameters

\[module name\] \(optional\)

          The name or a part of the name that will be searched through all the modules and only those which match will be showed.

### Examples

The following command show all the modules in the system.

```cpp
HyperDbg >lm
start                   size    name            path

fffff80434200000        11235328        ntoskrnl.exe    \SystemRoot\system32\ntoskrnl.exe
fffff8043415c000        671744  hal.dll \SystemRoot\system32\hal.dll
fffff80437a00000        45056   kd.dll  \SystemRoot\system32\kd.dll
fffff80437a10000        2101248 mcupdate_GenuineIntel.dll       \SystemRoot\system32\mcupdate_GenuineIntel.dll
fffff80437c70000        393216  msrpc.sys       \SystemRoot\System32\drivers\msrpc.sys
fffff80437c40000        172032  ksecdd.sys      \SystemRoot\System32\drivers\ksecdd.sys
fffff80437c20000        69632   werkernel.sys   \SystemRoot\System32\drivers\werkernel.sys
fffff80437d10000        425984  CLFS.SYS        \SystemRoot\System32\drivers\CLFS.SYS
fffff80437ce0000        159744  tm.sys  \SystemRoot\System32\drivers\tm.sys
fffff80437d80000        106496  PSHED.dll       \SystemRoot\system32\PSHED.dll
fffff80437da0000        45056   BOOTVID.dll     \SystemRoot\system32\BOOTVID.dll
fffff80437ec0000        462848  FLTMGR.SYS      \SystemRoot\System32\drivers\FLTMGR.SYS
fffff80437db0000        1069056 clipsp.sys      \SystemRoot\System32\drivers\clipsp.sys
fffff80437f40000        57344   cmimcext.sys    \SystemRoot\System32\drivers\cmimcext.sys
fffff80437f50000        49152   ntosext.sys     \SystemRoot\System32\drivers\ntosext.sys

...
```

The following example shows the modules that contain "**nt"** in their path or name.

```c
HyperDbg >lm nt
start                   size    name            path

fffff80434200000        11235328        ntoskrnl.exe    \SystemRoot\system32\ntoskrnl.exe
fffff80437a10000        2101248 mcupdate_GenuineIntel.dll       \SystemRoot\system32\mcupdate_GenuineIntel.dll
fffff80437f50000        49152   ntosext.sys     \SystemRoot\System32\drivers\ntosext.sys
fffff804382b0000        106496  SgrmAgent.sys   \SystemRoot\system32\drivers\SgrmAgent.sys
fffff804383c0000        372736  intelpep.sys    \SystemRoot\System32\drivers\intelpep.sys

...
```

### IOCTL

This function work by calling **NtQuerySystemInformation** and does not gets the address from the kernel so it doesn't have any IOCTL.

### **Remarks**

None

### Requirements

None

### Related

None


---
description: Description of 'lm' command in HyperDbg.
---

# lm (view loaded modules)

### Command

> lm

### Syntax

> lm
>
> lm \[m Name (string)] \[pid ProcessId (hex)] \[Filter (string)]

### Description

Shows the loaded modules' base address, size, name, full path.

### Parameters

**\[m Name (string)] (optional)**

The name or a part of the name that will be searched through all the modules and only those which match will be shown. The search is case-insensitive.

**\[pid ProcessId (hex)] (optional)**

The Process Id of the target process in which the user-mode modules are shown. The process id only makes sense in user-mode modules, and this parameter will be ignored for kernel-mode modules.

{% hint style="info" %}
By default, when you didn't use the **pid** parameter, if you are attached to a process in the user-mode debugger, this command shows the user-mode modules of the debuggee process; otherwise, it shows the user-mode modules of the process of HyperDbg.
{% endhint %}

**\[Filter (string)] (optional)**

Can be one of these values :

**km**: only shows the kernel-mode modules.

**um**: only shows the user-mode modules.

### Examples

The following command shows all the user-mode and kernel-mode modules.

```cpp
HyperDbg> lm
user mode
start                   entrypoint              path

....
00007ffd885e0000        00007ffd885f5600        C:\Windows\System32\ADVAPI32.dll
00007ffd86fc0000        00007ffd86fc7850        C:\Windows\System32\msvcrt.dll
00007ffd87df0000        00007ffd87e0cd20        C:\Windows\System32\sechost.dll
00007ffd86e60000        00007ffd86ebdfb0        C:\Windows\System32\RPCRT4.dll
00007ffd88cb0000        00007ffd88cba7a0        C:\Windows\System32\SHLWAPI.dll
00007ffd886a0000        00007ffd886b4300        C:\Windows\System32\WS2_32.dll
...

==============================================================================

kernel mode
start                   size    name                            path

fffff801`63000000       1046000 ntoskrnl.exe                    \SystemRoot\system32\ntoskrnl.exe
fffff801`5fa40000       6000    hal.dll                         \SystemRoot\system32\hal.dll
fffff801`5fab0000       49000   kdnet.dll                       \SystemRoot\system32\kdnet.dll
fffff801`5fa50000       5c000   kd_02_8086.dll                  \SystemRoot\system32\kd_02_8086.dll
...
```

This command only shows the **user-mode** modules.

```clike
HyperDbg> lm um
user mode
start                   entrypoint              path

...
00007ffd88d50000        0000000000000000        C:\Windows\SYSTEM32\ntdll.dll
00007ffd88860000        00007ffd888770d0        C:\Windows\System32\KERNEL32.DLL
00007ffd865f0000        00007ffd865f92c0        C:\Windows\System32\KERNELBASE.dll
00007ffd83d90000        00007ffd83da0880        C:\Windows\SYSTEM32\apphelp.dll
...
```

The following command shows the **user-mode** modules of the process with process id equal to **1240** that contains "_**kernel**_" in their path.

```clike
HyperDbg> lm um m kernel pid 1240
user mode
start                   entrypoint              path

00007ffd88860000        00007ffd888770d0        C:\Windows\System32\KERNEL32.DLL
00007ffd865f0000        00007ffd865f92c0        C:\Windows\System32\KERNELBASE.dll

```

The following example shows the **kernel-mode** modules that contain "_**nt**_**"** in their path or name.

```c
HyperDbg> lm km m nt
kernel mode
start                   size    name                            path

fffff801`63000000       1046000 ntoskrnl.exe                    \SystemRoot\system32\ntoskrnl.exe
fffff801`5f7b0000       28f000  mcupdate_GenuineIntel.dll       \SystemRoot\system32\mcupdate_GenuineIntel.dll
fffff801`65330000       c000    ntosext.sys                     \SystemRoot\System32\drivers\ntosext.sys
fffff801`656a0000       1a000   SgrmAgent.sys                   \SystemRoot\system32\drivers\SgrmAgent.sys
fffff801`657c0000       6b000   intelpep.sys                    \SystemRoot\System32\drivers\intelpep.sys
fffff801`65850000       b000    IntelTA.sys                     \SystemRoot\System32\drivers\IntelTA.sys
fffff801`658a0000       b000    intelide.sys                    \SystemRoot\System32\drivers\intelide.sys
fffff801`65b80000       1e000   mountmgr.sys                    \SystemRoot\System32\drivers\mountmgr.sys
fffff801`65e10000       2d9000  Ntfs.sys                        \SystemRoot\System32\Drivers\Ntfs.sys
fffff801`66640000       7f000   fwpkclnt.sys                    \SystemRoot\System32\drivers\fwpkclnt.sys
fffff801`67c10000       b000    vmgencounter.sys                \SystemRoot\System32\drivers\vmgencounter.sys
fffff801`67c50000       40000   intelppm.sys                    \SystemRoot\System32\drivers\intelppm.sys

```

### IOCTL

This function work by calling **NtQuerySystemInformation** and does not gets the address from the kernel, so it doesn't have any IOCTL.

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

None

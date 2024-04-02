---
description: Getting System-Calls
---

# Intercepting All SYSCALLs

In HyperDbg, you are able to [intercept all syscall](https://docs.hyperdbg.org/design/features/vmm-module/design-of-syscall-and-sysret)s or special a syscall.

For this purpose, you have to use the [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall) command. You can also use the [!sysret](https://docs.hyperdbg.org/commands/extension-commands/sysret) too.

There is a list of syscalls available [here](https://j00ru.vexillium.org/syscalls/nt/64/). You can find win32k syscalls [here](https://j00ru.vexillium.org/syscalls/win32k/64/).

For example, in **Windows 10 2004**, the syscall number for **NtCreateFile** is **0x55**.

We want to intercept all the times that a process with pid `2f4c` in our system tries to open a file, so we use the following command.

```c
HyperDbg> !syscall 55 pid 2f4c
```

We might even want to monitor all processes. For example, we want to intercept whenever any process uses **NtFreezeRegistry** (syscall number **0xee**).

```c
HyperDbg> !syscall ee
```

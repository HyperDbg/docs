---
description: Intercepting Exceptions, Interrupts, and MSRs
---

# Identifying System Behavior

In order to detect system behavior, we have 3 factors for this example. The first factor is intercepting the first 32 entries of IDT (Interrupt Descriptor Table). We use the [!exception](https://docs.hyperdbg.org/commands/extension-commands/exception) command for this purpose.

For instance, if we want to break on **division-by-zero** on process id **0x490**.

```c
HyperDbg> !exception 0x0 pid 490
```

If we want to monitor external-interrupts (IDT index from **0x21** to **0xff**), we use the [!interrupt](https://docs.hyperdbg.org/commands/extension-commands/interrupt) command.

Imagine we want to break on entry **0x25** of IDT.

```c
HyperDbg> !interrupt 0x25
```

The last factor is the system-wide monitoring of the execution of RDMSR and WRMSR. We use the [!msrread](https://docs.hyperdbg.org/commands/extension-commands/msrread) and the [!msrwrite](https://docs.hyperdbg.org/commands/extension-commands/msrwrite) commands.

For example, MSR **0xc0000082 (LSTAR)** is one of the MSRs used by malware and rootkits.

If we want to break on RDMSR to MSR **0xc0000082**.

```c
HyperDbg> !msrread 0xc0000082
```

If we want to break on WRMSR to MSR **0xc0000082**.

```c
HyperDbg> !msrwrite 0xc0000082
```

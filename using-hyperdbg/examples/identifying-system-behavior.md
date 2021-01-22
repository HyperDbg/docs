---
description: 'Intercepting Exceptions, Interrupts, and MSRs'
---

# Identifying System Behavior

In order to detect system behavior, we have 3 factors for this example. The first factor is intercepting first 32 entries of IDT \(Interrupt Descriptor Table\). We use [!exception](https://docs.hyperdbg.com/commands/extension-commands/exception) command for this purpose.

If we want to break on **division-by-zero** on process id **0x490**.

```c
HyperDbg> !exception 0x0 pid 490
```

Imagine we want to break on entry **0x25** of IDT.

```c
HyperDbg> !interrupt 0x25
```

Imagine we want to break on all **RDMSRs**.

```c
HyperDbg> !msrread
```

If we want to break on MSR **0xc0000082**.

```c
HyperDbg> !msrread 0xc0000082  
```

Imagine we want to break on all **WRMSRs**.

```c
HyperDbg> !msrwrite
```

If we want to break on MSR **0xc0000082**.

```c
HyperDbg> !msrwrite 0xc0000082
```

### 


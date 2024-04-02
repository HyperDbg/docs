---
description: Design of !exception and !interrupt command
---

# Design of !exception & !interrupt

[!exception](https://docs.hyperdbg.org/commands/extension-commands/exception) and [!interrupt](https://docs.hyperdbg.org/commands/extension-commands/interrupt) commands are used to hook the **exceptions**, **faults**, **aborts**, and **external-interrupts**.

**!exception** command uses the **Exception Bitmap** field of VMCS, which is a mask that if you set a special bit on it then every time that an exception generated (on that special IDT entry which we set the mask), it causes vm-exit.

It's clear that only those entries that you want will cause a vm-exit and not all entries.

It works on the first 32 entries of IDT or entries between **0x0** to **0x1f**.

All vm-exits are handled in the same way, but page-faults (**#PF**) are different. In those cases, **HyperDbg** also the **cr2** register too.

**!interrupt**, on the other hand, is different. There is a bit in **pin-based vmx controls**, which cause vm-exit on all external-interrupts (starting from 0x20 to 0xff); thus, if you want just an entry above the **0x1f**, then all of the external-interrupts cause vm-exit and **HyperDbg** manages them, so it's substantially slower.

There also other considerations for emulating external-interrupts. For example, the target guest might not be in an **interruptible-state** (e.g., **RFLAG.IF** bit is not set), so we have to save the interrupt details somewhere else and wait for a window to open (**interrupt-window exiting**).

Whenever the guest is in an **interruptible-state**, it causes vm-exit (because of **interrupt-window exiting** bit), and we re-inject all the accumulated interrupts.

You should not try to intercept and handle all external-interrupts by yourself; just choose one entry. It is because some interrupts like clock-cycle are at a high-rate, and if you intercept them, then Windows timing-clock will be dead, and you'll end up with a BSOD.

---
description: Description of '!ioapic' command in HyperDbg.
---

# !ioapic (dump I/O APIC)

### Command

> !ioapic

### Syntax

> !ioapic

### Description

Dumps I/O APIC (Advanced Programmable Interrupt Controller) items.

{% hint style="info" %}
In order to get Local APIC entries, you need to use the '[!apic](https://docs.hyperdbg.org/commands/extension-commands/apic)' command.
{% endhint %}

### Parameters

None

### Examples

The following command dumps I/O APIC entries.

```c
2: kHyperDbg> !ioapic
IoApic @ fec00000  ID:4 (20)  Arb:4000000        I/O APIC VA: ffffa901`203e1000
Inti00.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti01.: 00770000`00000080  Vec:80  FixedDel  PhysDest:00       edge
Inti02.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti03.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti04.: 007b0000`00000060  Vec:60  FixedDel  PhysDest:00       edge
Inti05.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti06.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti07.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti08.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti09.: 00110000`0000a0b0  Vec:B0  FixedDel  PhysDest:00       level
Inti0A.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti0B.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti0C.: 00790000`00000070  Vec:70  FixedDel  PhysDest:00       edge
Inti0D.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti0E.: 00570000`000000b2  Vec:B2  FixedDel  PhysDest:00       edge
Inti0F.: 00590000`000000a2  Vec:A2  FixedDel  PhysDest:00       edge
Inti10.: 00710000`0000a063  Vec:63  FixedDel  PhysDest:00       level
Inti11.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti12.: 00750000`0000a0b4  Vec:B4  FixedDel  PhysDest:00       level
Inti13.: 00730000`0000a053  Vec:53  FixedDel  PhysDest:00       level
Inti14.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti15.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti16.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
Inti17.: 00000000`000100ff  Vec:FF  FixedDel  PhysDest:00       edge         masked
```

### SDK

To get the I/O APIC details, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_get_io_apic(IO_APIC_ENTRY_PACKETS * io_apic);
```

### Remarks

Starting from **v0.11**, this command was added to the HyperDbg debugger.

{% hint style="warning" %}
This command operates under the assumption that your computer has a **single** I/O APIC, which might not apply to multiprocessor systems, where multiple I/O APICs are common. Additionally, it assumes that the I/O APIC has not been relocated from its default address (e.g., by the BIOS or operating system) and is positioned at the standard location, **0xFEC00000**.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!apic (dump local APIC entries in XAPIC and X2APIC modes)](https://docs.hyperdbg.org/commands/extension-commands/apic)

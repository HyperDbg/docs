---
description: Description of '!apic' command in HyperDbg.
---

# !apic (dump local APIC entries in XAPIC and X2APIC modes)

### Command

> !apic

### Syntax

> !apic

### Description

Dumps Local APIC (Advanced Programmable Interrupt Controller) items in XAPIC and X2APIC modes.

{% hint style="info" %}
In order to get I/O APIC entries, you need to use the '[!ioapic](https://docs.hyperdbg.org/commands/extension-commands/ioapic)' command.
{% endhint %}

### Parameters

None

### Examples

The following command dumps local APIC items.

```c
2: kHyperDbg> !apic
***  (X2APIC Mode) PHYSICAL LAPIC ID = 0, Ver = 0x60015, MaxLvtEntry = 6, DirectedEOI = P0/E0, (SW: 'Enabled')
     -> TPR = 0x00000000,  PPR = 0x00000000
     -> LDR = 0x00000004,  SVR = 0x000001df,  Err = 0x00000000
     -> LVT_INT0 = 0x000100d8,  LVT_INT1 = 0x00000400
     -> LVT_CMCI = 0x00000035,  LVT_PMCR = 0x000000fe
     -> LVT_TMR  = 0x000300fd,  LVT_TSR  = 0x00010000
     -> LVT_ERR  = 0x000000e2
     -> InitialCount = 0x00000000, CurrentCount = 0x00000000, DivideConfig = 0x0000000a
ISR :
TMR : 0x53 0x63
IRR : 0x60 0x62 0xd1
```

### SDK

To get the local APIC details, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_get_local_apic(PLAPIC_PAGE local_apic, BOOLEAN * is_using_x2apic);
```

### Remarks

Starting from **v0.11**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!ioapic (dump I/O APIC)](https://docs.hyperdbg.org/commands/extension-commands/ioapic)

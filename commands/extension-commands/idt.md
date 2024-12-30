---
description: Description of '!idt' command in HyperDbg.
---

# !idt (show Interrupt Descriptor Table entries)

> !idt

### Syntax

> !idt \[IdtEntry (hex)]

### Description

Shows Interrupt Descriptor Table (IDT) entries.

### Parameters

**\[IdtEntry (hex)]**

The target interrupt/exception/fault entry.

{% hint style="info" %}
If you don't specify the **IdtEntry**, then all 256 entries of IDT will be shown.
{% endhint %}

### Examples

The following command shows the IDT entries of the target debuggee.

```c
3: kHyperDbg> !idt
IDT Entries:

IDT[0x0:0]      : fffff801`4ce23b00     ntkrnlmp!KiDivideErrorFault
IDT[0x1:1]      : fffff801`4ce23e40     ntkrnlmp!KiDebugTrapOrFault
IDT[0x2:2]      : fffff801`4ce24440     ntkrnlmp!KiNmiInterruptStart
IDT[0x3:3]      : fffff801`4ce249c0     ntkrnlmp!KiBreakpointTrap
IDT[0x4:4]      : fffff801`4ce24d00     ntkrnlmp!KiOverflowTrap
IDT[0x5:5]      : fffff801`4ce25040     ntkrnlmp!KiBoundFault
IDT[0x6:6]      : fffff801`4ce25700     ntkrnlmp!KiInvalidOpcodeFault
IDT[0x7:7]      : fffff801`4ce25d80     ntkrnlmp!KiNpxNotAvailableFault
IDT[0x8:8]      : fffff801`4ce26140     ntkrnlmp!KiDoubleFaultAbort
IDT[0x9:9]      : fffff801`4ce26480     ntkrnlmp!KiNpxSegmentOverrunAbort
IDT[0xa:10]     : fffff801`4ce267c0     ntkrnlmp!KiInvalidTssFault
IDT[0xb:11]     : fffff801`4ce26b00     ntkrnlmp!KiSegmentNotPresentFault
IDT[0xc:12]     : fffff801`4ce26ec0     ntkrnlmp!KiStackFault
IDT[0xd:13]     : fffff801`4ce27240     ntkrnlmp!KiGeneralProtectionFault
IDT[0xe:14]     : fffff801`4ce275c0     ntkrnlmp!KiPageFault
IDT[0xf:15]     : fffff801`4ce1a268     ntkrnlmp!KxUnexpectedInterrupt0+0x78
IDT[0x10:16]    : fffff801`4ce27dc0     ntkrnlmp!KiFloatingErrorFault
IDT[0x11:17]    : fffff801`4ce28180     ntkrnlmp!KiAlignmentFault
IDT[0x12:18]    : fffff801`4ce284c0     ntkrnlmp!KiMcheckAbort
IDT[0x13:19]    : fffff801`4ce29280     ntkrnlmp!KiXmmException
IDT[0x14:20]    : fffff801`4ce29680     ntkrnlmp!KiVirtualizationException
IDT[0x15:21]    : fffff801`4ce29d80     ntkrnlmp!KiControlProtectionFault
IDT[0x16:22]    : fffff801`4ce1a2a0     ntkrnlmp!KxUnexpectedInterrupt0+0xb0
IDT[0x17:23]    : fffff801`4ce1a2a8
IDT[0x18:24]    : fffff801`4ce1a2b0
IDT[0x19:25]    : fffff801`4ce1a2b8
IDT[0x1a:26]    : fffff801`4ce1a2c0
IDT[0x1b:27]    : fffff801`4ce1a2c8
IDT[0x1c:28]    : fffff801`4ce1a2d0
IDT[0x1d:29]    : fffff801`4ce1a2d8
IDT[0x1e:30]    : fffff801`4ce1a2e0
IDT[0x1f:31]    : fffff801`4ce1c6d0     ntkrnlmp!KiApcInterrupt
IDT[0x20:32]    : fffff801`4ce1ea00     ntkrnlmp!KiSwInterrupt
IDT[0x21:33]    : fffff801`4ce1a2f8     ntkrnlmp!KxUnexpectedInterrupt0+0x108
IDT[0x22:34]    : fffff801`4ce1a300
IDT[0x23:35]    : fffff801`4ce1a308
```

Another example of using this command to see a single IDT entry.

```clike
3: kHyperDbg> !idt e
IDT[0xe:14] : fffff801`4ce275c0 ntkrnlmp!KiPageFault
```

### SDK

To get the IDT details, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_get_idt_entry(INTERRUPT_DESCRIPTOR_TABLE_ENTRIES_PACKETS * idt_packet);
```

### Remarks

Starting from **v0.12**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

None

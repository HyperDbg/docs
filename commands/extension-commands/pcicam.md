---
description: Description of '!pcicam' command in HyperDbg.
---

# !pcicam (dump the PCI/PCI-e configuration space)

> !pcicam

### Syntax

> !pcicam \[Bus (hex)] \[Device (hex)] \[Function (hex)]
>
> !pcicam \[Bus (hex)] \[Device (hex)] \[Function (hex)] \[Dump (string)]

### Description

Dumps the PCI configuration space (CAM) for a given device.

### Parameters

**\[Bus (hex)]**

The PCI/PCI-e Bus number.

**\[Device (hex)]**

The PCI/PCI-e Device number.

**\[Function (hex)]**

The PCI/PCI-e Function number.

**\[Dump (string)] (optional)**

**x:** dump the PCI/PCI-e CAM in hexadecimal format.

### Examples

The following command interprets the PCI/PCI-e CAM fields of the **Bus:3**, **Device:0**, and **Function:0**.

```c
6: kHyperDbg> !pcicam 3 0 0
PCI configuration space (CAM) for device 0000:03:00:0

Common Header:
VID:DID: 8086:10d3
Vendor Name: Intel Corporation
Device Name: 82574L Gigabit Network Connection
Command: 0007
  Memory Space: 1
  I/O Space: 1
Status: 0010
Revision ID: 00
Class Code: 70eeac0b
CacheLineSize: 10
PrimaryLatencyTimer: 00
HeaderType: Endpoint (00)
  Multi-function Device: False
Bist: 00

Device Header:
BAR0
 BAR Type: MMIO
 BAR: fea00000
 BAR (actual): fea00000
 Prefetchable: False
 Addressable range: 0-00000000
BAR1
 BAR Type: MMIO
 BAR: fea20000
 BAR (actual): fea20000
 Prefetchable: False
 Addressable range: 0-00000000
BAR2
 BAR Type: Port IO
 BAR: 00004001
 BAR (actual): 00004000
 Reserved: 0
BAR3
 BAR Type: MMIO
 BAR: fea40000
 BAR (actual): fea40000
 Prefetchable: False
 Addressable range: 0-00000000
BAR4 [disabled]
 BAR Type: MMIO
 BAR: 00000000
 BAR (actual): 00000000
 Prefetchable: False
 Addressable range: 0-00000000
Cardbus CIS Pointer: 00000000
Subsystem Vendor ID: 15ad
Subsystem ID: 07d0
ROM BAR: ffff0000
Capabilities Pointer: c8
Reserved (0xD): 70eeac37
Reserved (0xE): 00000000
Interrupt Line: ff
Interrupt Pin: 01
Min Grant: 00
Max latency: 00

```

The following command interprets the PCI/PCI-e CAM fields of a **PCI Root Port** with the BDF number of **Bus:3**, **Device:0**, and **Function:0**.

```clike
6: kHyperDbg> !pcicam 0 17 7
PCI configuration space (CAM) for device 0000:00:17:7

Common Header:
VID:DID: 15ad:07a0
Vendor Name: VMware
Device Name: PCI Express Root Port
Command: 0006
Status: 0010
Revision ID: 01
Class Code: 70eeac0b
CacheLineSize: 00
PrimaryLatencyTimer: 00
HeaderType: PCI-to-PCI Bridge (81)
  Multi-function Device: True
Bist: 00

Device Header:
BAR0: 00000000
BAR1: 00000000
Primary Bus Number: 00
Secondary Bus Number: 1a
Subordinate Bus Number: 1a
Secondary Latency Timer: 00
I/O Base: f0
I/O Limit: 00
Secondary Status: 0000
Memory Base: fd30
Memory Limit: fd30
Prefetchable Memory Base: f8d1
Prefetchable Memory Limit: f8d1
Prefetchable Base Upper 32 Bits: 00000000
Prefetchable Limit Upper 32 Bits: 00000000
I/O Base Upper 16 Bits: 0000
I/O Limit Upper 16 Bits: 0000
Capability Pointer: 40
Reserved: 70eeac37
ROM BAR: 00000000
Interrupt Line: ff
Interrupt Pin: 00
Bridge Control: 0000
```

The following command dumps the PCI/PCI-e CAM of the **Bus:3**, **Device:0**, and **Function:0** in the hexadecimal format.

```clike
6: kHyperDbg> !pcicam 3 0 0 x
PCI configuration space (CAM) for device 0000:03:00:0
    00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f
00: 86 80 d3 10 07 00 10 00 00 00 00 02 10 00 00 00 ................
10: 00 00 a0 fe 00 00 a2 fe 01 40 00 00 00 00 a4 fe ................
20: 00 00 00 00 00 00 00 00 00 00 00 00 ad 15 d0 07 ................
30: 00 00 ff ff c8 00 00 00 00 00 00 00 ff 01 00 00 ................
40: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
60: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
70: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
a0: 11 00 04 00 03 00 00 00 03 20 00 00 00 00 00 00 ................
b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
c0: 00 00 00 00 00 00 00 00 01 d0 22 c8 00 20 00 14 ................
d0: 05 e0 80 00 00 00 00 00 00 00 00 00 00 00 00 00 ................
e0: 10 a0 01 00 c1 8c 00 00 00 00 00 00 02 06 00 00 ................
f0: 00 00 02 02 00 00 00 00 00 00 00 00 00 00 00 00 ................
```

### SDK

None

### Remarks

Starting from **v0.13**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!pcitree (show PCI/PCI-e device tree)](https://docs.hyperdbg.org/commands/extension-commands/pcitree)

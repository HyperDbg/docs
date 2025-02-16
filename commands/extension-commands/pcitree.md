---
description: Description of '!pcitree' command in HyperDbg.
---

# !pcitree (show PCI/PCI-e device tree)

> !pcitree

### Syntax

> !pcitree

### Description

Shows PCI/PCIe device tree.

### Parameters

None

### Examples

The following command shows the PCI and PCI Express device trees.

```c
HyperDbg> !pcitree
DBDF         | VID:DID   | Vendor Name       | Device Name
----------------------------------------------------------------------
0000:00:00:0 | 8086:a71b | Intel Corporation | N/A
0000:00:02:0 | 8086:a7ad | Intel Corporation | Raptor Lake-U [Intel Graphics]
0000:00:04:0 | 8086:a71d | Intel Corporation | Raptor Lake Dynamic Platform and Thermal Framework Processor Participant
0000:00:06:0 | 8086:a74d | Intel Corporation | Raptor Lake PCIe 4.0 Graphics Port
0000:00:08:0 | 8086:a74f | Intel Corporation | GNA Scoring Accelerator module
0000:00:0d:0 | 8086:a71e | Intel Corporation | Raptor Lake-P Thunderbolt 4 USB Controller
0000:00:14:0 | 8086:51ed | Intel Corporation | Alder Lake PCH USB 3.2 xHCI Host Controller
0000:00:14:2 | 8086:51ef | Intel Corporation | Alder Lake PCH Shared SRAM
0000:00:15:0 | 8086:51e8 | Intel Corporation | Alder Lake PCH Serial IO I2C Controller #0
0000:00:15:1 | 8086:51e9 | Intel Corporation | Alder Lake PCH Serial IO I2C Controller #1
0000:00:16:0 | 8086:51e0 | Intel Corporation | Alder Lake PCH HECI Controller
0000:00:1c:0 | 8086:51bf | Intel Corporation | Alder Lake PCH-P PCI Express Root Port #9
0000:00:1f:0 | 8086:519d | Intel Corporation | Raptor Lake LPC/eSPI Controller
0000:00:1f:3 | 8086:51ca | Intel Corporation | Raptor Lake-P/U/H cAVS
0000:00:1f:4 | 8086:51a3 | Intel Corporation | Alder Lake PCH-P SMBus Host Controller
0000:00:1f:5 | 8086:51a4 | Intel Corporation | Alder Lake-P PCH SPI Controller
0000:01:00:0 | 1e0f:000c | KIOXIA Corporation | NVMe SSD Controller BG5 (DRAM-less)
0000:02:00:0 | 10ec:b852 | Realtek Semiconductor Co., Ltd. | RTL8852BE PCIe 802.11ax Wireless Network Controller
```

Another example of using this command in a VMware Workstation guest.

```clike
0: kHyperDbg> !pcitree
DBDF         | VID:DID   | Vendor Name       | Device Name
----------------------------------------------------------------------
0000:00:00:0 | 8086:7190 | Intel Corporation | 440BX/ZX/DX - 82443BX/ZX/DX Host bridge
0000:00:01:0 | 8086:7191 | Intel Corporation | 440BX/ZX/DX - 82443BX/ZX/DX AGP bridge
0000:00:07:0 | 8086:7110 | Intel Corporation | 82371AB/EB/MB PIIX4 ISA
0000:00:07:1 | 8086:7111 | Intel Corporation | 82371AB/EB/MB PIIX4 IDE
0000:00:07:3 | 8086:7113 | Intel Corporation | 82371AB/EB/MB PIIX4 ACPI
0000:00:07:7 | 15ad:0740 | VMware            | Virtual Machine Communication Interface
0000:00:0f:0 | 15ad:0405 | VMware            | SVGA II Adapter
0000:00:11:0 | 15ad:0790 | VMware            | PCI bridge
0000:00:15:0 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:1 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:2 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:3 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:4 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:5 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:6 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:15:7 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:0 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:1 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:2 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:3 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:4 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:5 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:6 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:16:7 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:0 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:1 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:2 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:3 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:4 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:5 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:6 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:17:7 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:0 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:1 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:2 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:3 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:4 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:5 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:6 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:00:18:7 | 15ad:07a0 | VMware            | PCI Express Root Port
0000:02:00:0 | 15ad:0774 | VMware            | USB1.1 UHCI Controller
0000:02:01:0 | 15ad:1977 | VMware            | HD Audio Controller
0000:02:02:0 | 15ad:0770 | VMware            | USB2 EHCI Controller
0000:02:03:0 | 15ad:07e0 | VMware            | SATA AHCI controller
0000:03:00:0 | 8086:10d3 | Intel Corporation | 82574L Gigabit Network Connection
0000:0b:00:0 | 15ad:077a | VMware            | N/A
0000:13:00:0 | 15ad:07f0 | VMware            | NVMe SSD Controller
```

### SDK

None

### Remarks

Starting from **v0.12**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!pcicam (dump the PCI/PCI-e configuration space)](https://docs.hyperdbg.org/commands/extension-commands/pcicam)

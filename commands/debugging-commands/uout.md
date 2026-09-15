---
description: Description of the 'uout' command in HyperDbg.
---

# uout (execute I/O OUT instruction in debuggee)

### Command

> uout

### Syntax

> uout \[Register (string)] \[Port (hex)] \[Value (hex)]

### Description

Executes the **OUT** instruction on the target debuggee to write a value to the specified I/O port (port-mapped I/O).

{% hint style="info" %}
This command executes the **OUT** instruction in the **target debuggee** (not the debugger). In local debugging mode, the debuggee and the debugger are the same machine, so the write is applied to the local processor's I/O space.
{% endhint %}

### Parameters

**\[Register (string)]**

The source register, which also determines the size of the **OUT** operation:

* `al` : writes a **byte** to the port
* `ax` : writes a **word** (2 bytes) to the port
* `eax` : writes a **double-word** (4 bytes) to the port

**\[Port (hex)]**

The I/O port address (hex or decimal), in the range `0x0000` to `0xffff`.

**\[Value (hex)]**

The value (hex or decimal) to write to the port.

### Examples

The following command writes the byte `0xed` to the keyboard controller port (`0x60`).

```c
HyperDbg> uout al 0x60 0xed
```

The following command writes the word `0x004d` to COM1 (`0x3f8`).

```c
HyperDbg> uout ax 0x3f8 0x004d
```

The following command writes the double-word (4 bytes) `0x80000000` to the PCI configuration address port (`0xcf8`).

```c
HyperDbg> uout eax 0xcf8 0x80000000
```

#### Writing PCI configuration space (PCI Configuration Mechanism #1)

A common use case for '**uout**' and '**uin**' is accessing the **PCI Configuration Mechanism #1**, which uses I/O ports `0xcf8` (configuration address) and `0xcfc` (configuration data).

You first write a 32-bit configuration-address value to `0xcf8` using '**uout**' and then read the corresponding 32-bit configuration-data value from `0xcfc` using '[uin](https://docs.hyperdbg.org/commands/debugging-commands/uin)'. The `0xcf8` value is **not** just the PCI offset; it encodes the **bus**, **device**, **function**, and **offset**:

```
31          24 23    16 15     11 10      8 7      2 1 0
+-------------+--------+---------+---------+--------+-+
| Enable = 1  |  Bus   | Device  | Function| Offset |0|
+-------------+--------+---------+---------+--------+-+
```

The configuration-address formula is:

```
address = 0x80000000
        | (bus      << 16)
        | (device   << 11)
        | (function << 8)
        | (offset   & 0xFC)
```

For example, to read the Vendor ID and Device ID (configuration offset `0x00`) of the device at **Bus 0**, **Device 0**, **Function 0**, the `0xcf8` value is `0x80000000`.

```c
2: kHyperDbg> uout eax 0xcf8 0x80000000
  port:          0x0cf8 (3320)
  register:      EAX
  entered value: 0x80000000

2: kHyperDbg> uin eax 0xcfc
  port:     0x0cfc (3324)
  register: eax
  result:   0x71928086
```

In this example, the result `0x71928086` means the **Vendor ID** is `8086` (Intel) and the **Device ID** is `7192`.

### SDK

To execute an **OUT** instruction in the target debuggee, use the following function in `libhyperdbg`:

```c
BOOLEAN
hyperdebg_u_out_instruction(DEBUGGER_USER_OUT_REQUEST_RESPONSE OutRequest);
```

### Remarks

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

{% hint style="warning" %}
Writing to arbitrary I/O ports can alter hardware state or destabilize the target machine. Make sure you know the effect of the port you're writing to.
{% endhint %}

### Requirements

None

### Related

[uin (execute I/O IN instruction in debuggee)](https://docs.hyperdbg.org/commands/debugging-commands/uin)

[ucpuid (execute CPUID instruction in debuggee)](https://docs.hyperdbg.org/commands/debugging-commands/ucpuid)

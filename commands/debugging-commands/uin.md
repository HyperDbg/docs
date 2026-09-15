---
description: Description of the 'uin' command in HyperDbg.
---

# uin (execute I/O IN instruction in debuggee)

### Command

> uin

### Syntax

> uin \[Register (string)] \[Port (hex)]

### Description

Executes the **IN** instruction on the target debuggee to read a value from the specified I/O port (port-mapped I/O) and shows the result.

{% hint style="info" %}
This command executes the **IN** instruction in the **target debuggee** (not the debugger). In local debugging mode, the debuggee and the debugger are the same machine, so the results reflect the local processor's I/O space.
{% endhint %}

### Parameters

**\[Register (string)]**

The destination register, which also determines the size of the **IN** operation:

* `al` : reads a **byte** from the port
* `ax` : reads a **word** (2 bytes) from the port
* `eax` : reads a **double-word** (4 bytes) from the port

**\[Port (hex)]**

The I/O port address (hex or decimal), in the range `0x0000` to `0xffff`.

### Examples

The following command reads a byte from the keyboard controller port (`0x60`).

```c
HyperDbg> uin al 0x60
```

The following command reads a word from COM1 (`0x3f8`).

```c
HyperDbg> uin ax 0x3f8
```

The following command reads a double-word (4 bytes) from the PCI configuration data port (`0xcf8`).

```c
HyperDbg> uin eax 0xcf8
```

A common use case for '**uin**' and '**uout**' is accessing the **PCI Configuration Mechanism**, which uses I/O ports `0xcf8` (configuration address) and `0xcfc` (configuration data).

You first write a 32-bit configuration-address value to `0xcf8` using '[uout](https://docs.hyperdbg.org/commands/debugging-commands/uout)' and then read the corresponding 32-bit configuration-data value from `0xcfc` using '**uin**'. The `0xcf8` value is **not** just the PCI offset; it encodes the **bus**, **device**, **function**, and **offset**:

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

To execute an **IN** instruction in the target debuggee, use the following function in `libhyperdbg`:

```c
BOOLEAN
hyperdebg_u_in_instruction(DEBUGGER_USER_IN_REQUEST_RESPONSE InRequest);
```

### Remarks

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[uout (execute I/O OUT instruction in debuggee)](https://docs.hyperdbg.org/commands/debugging-commands/uout)

[ucpuid (execute CPUID instruction in debuggee)](https://docs.hyperdbg.org/commands/debugging-commands/ucpuid)

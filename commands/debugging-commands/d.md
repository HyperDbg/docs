---
description: Description of 'db, dc, dd, dq' commands in HyperDbg.
---

# db, dc, dd, dq (read virtual memory)

### Command

> db : read memory as Byte values and ASCII characters
>
> dc : read memory as Double-word values (4 bytes) and ASCII characters
>
> dd : read memory as Double-word values (4 bytes)
>
> dq : read memory as Quad-word values (8 bytes)

### Syntax

> db \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dc \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dd \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dq \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]

### Description

Shows the **virtual** address memory content in hex form.

### Parameters

**\[Address (hex)]**

The **virtual** address of where we want to read its memory.

**\[l Length (hex)] (optional)**

The length (byte) in hex format.

**\[pid ProcessId (hex)] (optional)**

The Process ID in hex format that we want to see the memory from its context (**cr3**).

{% hint style="info" %}
If you don't specify the **pid**, then the default **pid** is the current process (HyperDbg) process layout of memory.
{% endhint %}

{% hint style="danger" %}
In the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), the **pid** (parameter) is ignored. If you want to view another process memory, use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command to switch to another process memory layout.
{% endhint %}

### Examples

The following command is used when we want to read the content of memory at `nt!Kd_DEFAULT_Mask` with length of `0x50`from the memory layout view of process (`4` a.k.a. system process) in a hex byte format.

```diff
HyperDbg> db Kd_DEFAULT_Mask l 50 pid 4
fffff801`63cf49ec  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf49fc  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a0c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a1c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a2c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

The following command is used when we want to read the content of memory at `nt!Kd_DEFAULT_Mask+@rax+10` with length of `0x30`from the memory layout view of process (`4` a.k.a. system process) in a hex byte format.

Note that `@rax` is **0x10** in this case.

```diff
HyperDbg> db Kd_DEFAULT_Mask+@rax+10 l 30 pid 4
fffff801`63cf4a0c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a1c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a2c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

The following command is used when we want to read the content of memory at ``fffff800`3ad6f010`` with length of `0x50` from the memory layout view of process (`4` a.k.a. system process) in a hex byte format.

```diff
HyperDbg> db fffff800`3ad6f010 l 50 pid 4
fffff800`3ad6f010  48 89 5C 24 08 48 89 6C 24 10 48 89 74 24 18 57  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  41 56 41 57 48 83 EC 30 65 48 8B 04 25 20 00 00  AVAWH..0eH.. ..
fffff800`3ad6f030  00 33 DB 44 0F B7 3D C5 3F 20 00 41 8B E8 48 8B  .3.D..=.? .A..H.
fffff800`3ad6f040  F2 89 5C 24 68 8B F9 4C 8B 88 C0 00 00 00 45 0F  ..\$h..L......E.
fffff800`3ad6f050  B7 B1 92 00 00 00 41 8B C6 44 8B C8 89 5C 24 20  ......A..D...\$
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word value (4 bytes) and ASCII characters format.

```diff
HyperDbg> dc fffff800`3ad6f010
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  57415641 30EC8348 048B4865 00002025  AVAWH..0eH.. ..
fffff800`3ad6f030  44DB3300 C53DB70F 4100203F 8B48E88B  .3.D..=.? .A..H.
fffff800`3ad6f040  245C89F2 4CF98B68 00C0888B 0F450000  ..\$h..L......E.
fffff800`3ad6f050  0092B1B7 8B410000 C88B44C6 20245C89  ......A..D...\$
fffff800`3ad6f060  48C58B44 CF8BD68B CC3693E8 C08548FF  D..H......6..H..
fffff800`3ad6f070  0038840F 8B480000 6C8B48D8 8B485824  ..8...H..H.l$XH.
fffff800`3ad6f080  5C8B48C3 8B485024 48602474 4130C483  .H.\$PH.t$`H..0A
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word values (4 bytes) format with the length of `0x10`.

```diff
HyperDbg> dd fffff800`3ad6f010 l 10
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Quad-word values (8 bytes) format.

```diff
0: kHyperDbg> dq fffff800`3ad6f010
fffff800`3ad6f010  6C894808`245C8948 57182474`89481024
fffff800`3ad6f020  30EC8348`57415641 00002025`048B4865
fffff800`3ad6f030  C53DB70F`44DB3300 8B48E88B`4100203F
fffff800`3ad6f040  4CF98B68`245C89F2 0F450000`00C0888B
fffff800`3ad6f050  8B410000`0092B1B7 20245C89`C88B44C6
fffff800`3ad6f060  CF8BD68B`48C58B44 C08548FF`CC3693E8
fffff800`3ad6f070  8B480000`0038840F 8B485824`6C8B48D8
fffff800`3ad6f080  8B485024`5C8B48C3 4130C483`48602474
```

### SDK

To read the memory in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
VOID
hyperdbg_u_show_memory_or_disassemble(DEBUGGER_SHOW_MEMORY_STYLE   style,
                                      UINT64                       address,
                                      DEBUGGER_READ_MEMORY_TYPE    memory_type,
                                      DEBUGGER_READ_READING_TYPE   reading_type,
                                      UINT32                       pid,
                                      UINT32                       size,
                                      PDEBUGGER_DT_COMMAND_OPTIONS dt_details);
```

### Remarks

* If you don't specify the length, the default length for HyperDbg is 0x80 Bytes.

{% hint style="warning" %}
Please note that you should specify a space between 'l' and the length in HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. (It's opposed to windbg).
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!db, !dc, !dd, !dq (read physical memory)](https://docs.hyperdbg.org/commands/extension-commands/d)

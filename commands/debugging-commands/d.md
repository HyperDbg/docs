---
description: Description of 'db, dc, dd, dq, dw, da, dds, dps, dqs' commands in HyperDbg.
---

# db, dc, dd, dq, dw, da, dds, dps, dqs (read virtual memory)

### Command

> db : read memory as Byte values and ASCII characters
>
> dc : read memory as Double-word values (4 bytes) and ASCII characters
>
> dd : read memory as Double-word values (4 bytes)
>
> dq : read memory as Quad-word values (8 bytes)
>
> dw : read memory as Word values (2 bytes)
>
> da : read memory as printable ASCII characters (null-terminated string)
>
> dds : read memory as Double-word values (4 bytes), with each value resolved to a symbol name (module!symbol+offset) where possible
>
> dps : read memory as pointer-sized values (8 bytes), with each value resolved to a symbol name (module!symbol+offset) where possible
>
> dqs : read memory as Quad-word values (8 bytes), with each value resolved to a symbol name (module!symbol+offset) where possible

### Syntax

> db \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dc \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dd \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dq \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dw \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> da \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dds \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dps \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]
>
> dqs \[Address (hex)] \[l Length (hex)] \[pid ProcessId (hex)]

### Description

Shows the **virtual** address memory content in hex form.

The 'dw' command shows **Word** (2 bytes) values. The 'da' command shows a **printable ASCII** (null-terminated) string. The 'dds', 'dps', and 'dqs' commands show **Double-word** (4 bytes), **pointer-sized** (8 bytes), and **Quad-word** (8 bytes) values respectively, and each value is additionally resolved to a symbol name (`module!symbol+offset`) where possible.

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

```c
HyperDbg> db Kd_DEFAULT_Mask l 50 pid 4
fffff801`63cf49ec  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf49fc  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a0c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a1c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a2c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

The following command is used when we want to read the content of memory at `nt!Kd_DEFAULT_Mask+@rax+10` with length of `0x30`from the memory layout view of process (`4` a.k.a. system process) in a hex byte format.

Note that `@rax` is **0x10** in this case.

```c
HyperDbg> db Kd_DEFAULT_Mask+@rax+10 l 30 pid 4
fffff801`63cf4a0c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a1c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
fffff801`63cf4a2c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

The following command is used when we want to read the content of memory at ``fffff800`3ad6f010`` with length of `0x50` from the memory layout view of process (`4` a.k.a. system process) in a hex byte format.

```c
HyperDbg> db fffff800`3ad6f010 l 50 pid 4
fffff800`3ad6f010  48 89 5C 24 08 48 89 6C 24 10 48 89 74 24 18 57  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  41 56 41 57 48 83 EC 30 65 48 8B 04 25 20 00 00  AVAWH..0eH.. ..
fffff800`3ad6f030  00 33 DB 44 0F B7 3D C5 3F 20 00 41 8B E8 48 8B  .3.D..=.? .A..H.
fffff800`3ad6f040  F2 89 5C 24 68 8B F9 4C 8B 88 C0 00 00 00 45 0F  ..\$h..L......E.
fffff800`3ad6f050  B7 B1 92 00 00 00 41 8B C6 44 8B C8 89 5C 24 20  ......A..D...\$
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word value (4 bytes) and ASCII characters format.

```c
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

```c
HyperDbg> dd fffff800`3ad6f010 l 10
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Quad-word values (8 bytes) format.

```c
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

The following example shows the content of memory at ``fffff805`c4305880`` in a Word values (2 bytes) format.

```c
2: kHyperDbg> dw fffff805`c4305880
fffff805`c4305880  4218 8E6A A88B FFFF 2258 9393 A88B FFFF
fffff805`c4305890  0000 0000 0000 0000 0000 0000 0000 0000
fffff805`c43058a0  0000 0000 0000 0000 0000 0000 0000 0000
fffff805`c43058b0  D1C0 C368 F805 FFFF 0000 0000 0000 0000
fffff805`c43058c0  1D50 8E67 A88B FFFF 0000 0000 0000 0000
fffff805`c43058d0  0000 0000 0000 0000 0000 0000 0000 0000
fffff805`c43058e0  0000 0000 0000 0000 0000 0000 0000 0000
fffff805`c43058f0  0000 0000 0000 0000 0000 0000 0000 0000
```

The following example shows the content of memory at `nt!Kd_DEFAULT_Mask` in a Word values (2 bytes) format with the length of `0x4`.

```c
2: kHyperDbg> dw nt!Kd_DEFAULT_Mask l 4
fffff805`c42663c8  0000 0000 ???? ???? ???? ???? ???? ????
```

The following example shows the content of memory at ``fffff805`c4305880`` as a printable ASCII (null-terminated) string.

```c
2: kHyperDbg> da fffff805`c4305880
fffff805`c4305880  .Bj.....X"......
```

The following example shows the content of memory at ``fffff805`c34da250`` in a Double-word values (4 bytes) format, with each value resolved to a symbol name where possible.

```c
2: kHyperDbg> dds fffff805`c34da250
fffff805`c34da250  03684A04
fffff805`c34da254  03936A00
fffff805`c34da258  091BF802
fffff805`c34da25c  071E6400
fffff805`c34da260  076EFF00
fffff805`c34da264  05D11800
fffff805`c34da268  076E6105
fffff805`c34da26c  07E5C106
```

The following example shows the content of memory at `nt!ExpFirmwareTableResource` in pointer-sized values (8 bytes) format, with each value resolved to a symbol name where possible.

```c
2: kHyperDbg> dps nt!ExpFirmwareTableResource
fffff805`c42feac0  fffff805`c42f5060  ntkrnlmp!PsLoadedModuleResource
fffff805`c42feac8  fffff805`c42fa680  ntkrnlmp!ExpSystemResourcesList
fffff805`c42fead0  00000000`00000000
fffff805`c42fead8  00000000`00000000
fffff805`c42feae0  00000000`00000000
fffff805`c42feae8  00000000`00000000
```

The following example shows the content of memory at ``fffff805`c42f5060`` in a Quad-word values (8 bytes) format, with each value resolved to a symbol name where possible.

```c
2: kHyperDbg> dqs fffff805`c42f5060
fffff805`c42f5060  fffff805`c4267bc0  ntkrnlmp!SepRmDbLock
fffff805`c42f5068  fffff805`c42feac0  ntkrnlmp!ExpFirmwareTableResource
fffff805`c42f5070  ffffa88b`922eb410
fffff805`c42f5078  00000000`00000000
fffff805`c42f5080  00000000`00000000
fffff805`c42f5088  00000000`00000000
fffff805`c42f5090  00000000`00000000
fffff805`c42f5098  00000000`00000000
fffff805`c42f50a0  00000019`00000000
fffff805`c42f50a8  00000000`00000000
fffff805`c42f50b0  ffffffff`00000000
fffff805`c42f50b8  00000000`00000000
fffff805`c42f50c0  00000000`00000000
fffff805`c42f50c8  fffff805`c3f72fff  ntkrnlmp!ExAllocatePool3+0x92f+0x68b
fffff805`c42f50d0  fffff805`c3f70000  ntkrnlmp!CmpPlatformSpecificField1+0xb88
fffff805`c42f50d8  00000000`00000e70
```

### SDK

To read the memory in the target debuggee, you need to use the following function in `libhyperdbg`:

```c
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

Starting from HyperDbg **v0.24**, the 'dw', 'da', 'dds', 'dps', and 'dqs' commands are available.

If you don't specify the length, the default length for HyperDbg is 0x80 Bytes.

{% hint style="warning" %}
Please note that you should specify a space between 'l' and the length in HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. (It's opposed to windbg).
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!db, !dc, !dd, !dq, !dw, !da, !dds, !dps, !dqs (read physical memory)](https://docs.hyperdbg.org/commands/extension-commands/d)

[dl (traverse through linked list using virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/dl)

[dt (display and map virtual memory to structures)](https://docs.hyperdbg.org/commands/debugging-commands/dt)

---
description: Description of the '!track' command in HyperDbg.
---

# !track (track and map function calls and returns to the symbols)

### Command

> !track : track function calls and return adderesses and map them to the symbols

### Syntax

> !track \[tree] \[Count (hex)]
>
> !track \[reg] \[Count (hex)]

### Description

Creates tracking records of function calls and return addresses by instrumenting instructions.

### Parameters

**\[tree]\(optional)**

The results will be shown in the '**tree**' style. (default)

**\[reg]\(optional)**

The results will be shown in '**tree**' style while registers are also shown.

**\[Count (hex)]**

The number of **instructions** to perform the instrument (This is not the number of calls.).

{% hint style="danger" %}
If you run this command without any parameter, it tries to run instructions in '**tree**' style with an unlimited number of instructions. You can pause the execution by pressing **CTRL+C**.
{% endhint %}

### Examples

The following example shows the tracking results (function calls and return addresses) for an unlimited number of instructions.

```diff
2: kHyperDbg> !track
┌── 00007ff7`1423f5a4
└── 00007ff7`142b9847
┌── 00000000`001172c6
└── kernel32!TlsGetValue+0x1b (00007ffc`7ca234db)
┌── 00000000`000467ae
└── kernel32!TlsGetValue+0x1b (00007ffc`7ca234db)
(10:36:41.951 - core : 2 - vmx-root? yes)        [+] Information (KdCheckGuestOperatingModeChanges:1405) | User-mode -> Kernel-mode

┌── nt!IopXxxControlFile (fffff800`3aec90b0)
│  ┌── nt!ObReferenceObjectByHandle (fffff800`3aeca970)
│  │  ┌── nt!ObpReferenceObjectByHandleWithTag (fffff800`3aeca9b0)
│  │  │  ┌── nt!ExpLookupHandleTableEntry (fffff800`3aecaff0)
│  │  │  └── nt!ExpLookupHandleTableEntry+0x3e (fffff800`3aecb02e)
│  │  │  ┌── nt!ExGetHandlePointer (fffff800`3aa92e40)
│  │  │  └── nt!ExGetHandlePointer+0xb (fffff800`3aa92e4b)
│  │  │  ┌── nt!KeLeaveCriticalRegionThread (fffff800`3aa92e00)
│  │  │  └── nt!KeLeaveCriticalRegionThread+0x20 (fffff800`3aa92e20)
│  │  └── nt!ObpReferenceObjectByHandleWithTag+0x281 (fffff800`3aecac31)
│  └── nt!ObReferenceObjectByHandle+0x32 (fffff800`3aeca9a2)
│  ┌── nt!ObReferenceObjectByHandle (fffff800`3aeca970)
│  │  ┌── nt!ObpReferenceObjectByHandleWithTag (fffff800`3aeca9b0)
│  │  │  ┌── nt!ExpLookupHandleTableEntry (fffff800`3aecaff0)
│  │  │  └── nt!ExpLookupHandleTableEntry+0x3e (fffff800`3aecb02e)
│  │  │  ┌── nt!ExGetHandlePointer (fffff800`3aa92e40)
│  │  │  └── nt!ExGetHandlePointer+0xb (fffff800`3aa92e4b)
│  │  │  ┌── nt!KeLeaveCriticalRegionThread (fffff800`3aa92e00)
│  │  │  └── nt!KeLeaveCriticalRegionThread+0x20 (fffff800`3aa92e20)
│  │  └── nt!ObpReferenceObjectByHandleWithTag+0x281 (fffff800`3aecac31)
│  └── nt!ObReferenceObjectByHandle+0x32 (fffff800`3aeca9a2)
│  ┌── nt!KeResetEvent (fffff800`3aa12c90)
│  └── nt!KeResetEvent+0x7b (fffff800`3aa12d0b)
│  ┌── nt!IoGetRelatedDeviceObject (fffff800`3aa92fa0)
│  └── nt!IoGetRelatedDeviceObject+0x33 (fffff800`3aa92fd3)
│  ┌── nt!guard_dispatch_icall (fffff800`3ac2a450)
│  │  ┌── fffff800`3f630d30
│  │  └── fffff800`3f630d3b
│  │  ┌── nt!ExGetPreviousMode (fffff800`3aac2300)
│  │  └── nt!ExGetPreviousMode+0x10 (fffff800`3aac2310)
│  │  ┌── nt!IoIs32bitProcess (fffff800`3aabd720)
│  │  └── nt!IoIs32bitProcess+0x34 (fffff800`3aabd754)
│  │  ┌── afd!AfdFastConnectionSend (fffff800`408c2b20)
│  │  │  ┌── nt!KeAcquireInStackQueuedSpinLock (fffff800`3aae3550)
│  │  │  └── nt!KeAcquireInStackQueuedSpinLock+0xb0 (fffff800`3aae3600)
│  │  │  ┌── nt!KeReleaseInStackQueuedSpinLock (fffff800`3aa8b450)
│  │  │  └── nt!KeReleaseInStackQueuedSpinLock+0xee (fffff800`3aa8b53e)
│  │  │  ┌── nt!ExAllocateFromLookasideListEx (fffff800`3aaf99d0)
│  │  │  │  ┌── nt!RtlpInterlockedPopEntrySList (fffff800`3ac296e0)
│  │  │  │  └── nt!ExpInterlockedPopEntrySListEnd+0xb (fffff800`3ac2970b)
│  │  │  └── nt!ExAllocateFromLookasideListEx+0x1b (fffff800`3aaf99eb)

...
```

If you want to see registers (i.e., parameters) to the '**call**' instructions, you can use the `reg` parameter.

```
4: kHyperDbg> !track reg
└── fffff806`3352128c
└── fffff806`33528e95
└── fffff806`334f27bb
┌── 00000000`00002dc6
RAX=0000000000000001 RBX=ffffc105a4a96230 RCX=ffffc105a4a96230
RDX=0000000000000000 RSI=0000000000000002 RDI=ffffc105a500be10
RIP=fffff806334fa89c RSP=fffffe836fc535a0 RBP=fffffe836fc537a1
R8=0000000000000000  R9=0000000000000000  R10=fffff806334f8b90
R11=0000000000000000 R12=0000000000000000 R13=0000000000000000
R14=0000000000000000 R15=ffffc105a70ced50 IOPL=00
OF 0  DF 0  IF 1  SF  0
ZF 1  PF 1  CF 0  AXF 0
CS 0010 SS 0018 DS 002b ES 002b FS 0053 GS 002b
RFLAGS=0000000000040246

│  ┌── nt!IopfCompleteRequest (fffff806`2149ca40)
RAX=0000000000000000 RBX=ffffc105a4a96230 RCX=ffffc105a4a96230
RDX=0000000000000000 RSI=0000000000000002 RDI=ffffc105a500be10
RIP=fffff8062149ca22 RSP=fffffe836fc53570 RBP=fffffe836fc537a1
R8=0000000000000000  R9=0000000000000000  R10=fffff806334f8b90
R11=0000000000000000 R12=0000000000000000 R13=0000000000000000
R14=0000000000000000 R15=ffffc105a70ced50 IOPL=00
OF 0  DF 0  IF 1  SF  0
ZF 1  PF 1  CF 0  AXF 0
CS 0010 SS 0018 DS 002b ES 002b FS 0053 GS 002b
RFLAGS=0000000000040246

...
```

### IOCTL

This command works the same as the [i (instrumentation step-in)](https://docs.hyperdbg.org/commands/debugging-commands/i) command. Please check the [IOCTL](https://docs.hyperdbg.org/commands/debugging-commands/i#ioctl) for this command.

### Remarks

This command will set a **Monitor Trap Flag** in debuggee and continue just the current executing core. After executing one instruction, it halts the debuggee again.

If the currently executing instruction is a **call** instruction, it will follow and enter the **call** instruction to find the recursive calls and returns (**ret** instructions).

If you load symbols and you don't want to see function names, you turn **addressconversion** off in the '[settings](https://docs.hyperdbg.org/commands/debugging-commands/settings)' command.

This command is able to track from user-mode to kernel-mode and kernel-mode to user-mode.

Beginning with HyperDbg **version 0.3**, the inclusion of support for this command has been implemented.

### Requirements

None

### Related

[i (instrumentation step-in)](https://docs.hyperdbg.org/commands/debugging-commands/i)

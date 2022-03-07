---
description: Description of 'k, kd, kq' commands in HyperDbg.
---

# k, kd, kq (display stack backtrace)

### Command

> k : show stack backtrace (only calls)
>
> kd : show stack backtrace (calls along with parameters in stack) in 32-bit format
>
> kq : show stack backtrace (calls along with parameters in stack) in 64-bit format

### Syntax

> k
>
> kd&#x20;
>
> kq&#x20;
>
> k \[base StackAddress (hex)] \[l Length (hex)]&#x20;
>
> kd \[base StackAddress (hex)] \[l Length (hex)]&#x20;
>
> kq \[base StackAddress (hex)] \[l Length (hex)]

### Description

Displays stack backtrace and optionally show the parameter in the stack.

### Parameters

**\[base StackAddress (hex)] (optional)**

If you want to specify any address other than the current `rsp` or `esp` register, you should set the base address by using this parameter.

**\[l Length (hex)] (optional)**

The length (byte) in hex format.

### Examples

The following command shows the callstack of the current thread.

```diff
2: kHyperDbg> k
[$+020]   fffff80168da7c86    (from <fffff80168da7c86>)
[$+050]   fffff80168dafab6    (from <fffff80168dafab6>)
[$+080]   fffff801678940aa    (from <fffff801678940aa>)
[$+150]   fffff8016362af7c    (from nt!ObpReferenceObjectByHandleWithTag+0x22c <fffff8016362af7c>)
[$+190]   fffff80163252f50    (from nt!IofCallDriver+0x50 <fffff80163252f50>)
[$+1d0]   fffff801635fd513    (from nt!IopSynchronousServiceTail+0x1a3 <fffff801635fd513>)
```

The following command shows the callstack of the current thread along with stack parameters.

```diff
2: kHyperDbg> kq
[$+000]      0000000000000000
[$+008]      0000000000000000
[$+010]      fffff80168daeb50 (addr <fffff80168daeb50>)
[$+018]      0000000000040246
[$+020]   fffff80168da7c86    (from <fffff80168da7c86>)
[$+028]      0000000000000000
[$+030]      ffff948cb9a02340 (addr <ffff948cb9a02340>)
[$+038]      0000000100000000
...
[$+150]   fffff8016362af7c    (from nt!ObpReferenceObjectByHandleWithTag+0x22c <fffff8016362af7c>)
[$+158]      0000000000000001
...
[$+188]      ffff948cc291cae0 (addr <ffff948cc291cae0>)
[$+190]   fffff80163252f50    (from nt!IofCallDriver+0x50 <fffff80163252f50>)
[$+198]      ffff948cbc327760 (addr <ffff948cbc327760>)
[$+1a0]      ffff948cc0314a80 (addr <ffff948cc0314a80>)
[$+1a8]      ffff930520206f49
[$+1b0]      0000000000000001
[$+1b8]      ffff948cbc327760 (addr <ffff948cbc327760>)
[$+1c0]      ffff948cc0314b98 (addr <ffff948cc0314b98>)
[$+1c8]      ffff948cc291cae0 (addr <ffff948cc291cae0>)
[$+1d0]   fffff801635fd513    (from nt!IopSynchronousServiceTail+0x1a3 <fffff801635fd513>)
..
[$+1f8]      0000000000000001
```

The following command shows the calls along with parameters (the base address is `@rbx-10`).

```diff
2: kHyperDbg> kq base @rbx-10
[$+000]      8d0ac9a500000000
[$+008]      0000000000000edc
[$+010]      0000200000000000
[$+018]      0000000000000000
[$+020]      0000000000000000
[$+028]      0000006700000000
[$+030]      5754444e02060000
[$+038]      fa3ced7b5a3515ff
[$+040]      0000000000000000
[$+048]      00000206c1f30004
[$+050]      ffff948cc06a9790 (addr <ffff948cc06a9790>)
```

The following command shows the callstack of the current thread in a 32-bit environment.

```diff
0: kHyperDbg> k
[$+004]   00a42274    (from <00a42274>)
[$+008]   7559fa27    (from <7559fa27>)
[$+018]   770775f2    (from <770775f2>)
[$+074]   770775bf    (from <770775bf>)
```

The following command shows the callstack of the current thread along with parameters in a 32-bit environment.

```diff
0: kHyperDbg> kd
[$+000]      010ffa18 (addr <010ffa18>)
[$+004]   00a42274    (from <00a42274>)
[$+008]   7559fa27    (from <7559fa27>)
[$+00c]      00f00000 (addr <00f00000>)
[$+010]      7559fa10 (addr <7559fa10>)
[$+014]      010ffa74 (addr <010ffa74>)
[$+018]   770775f2    (from <770775f2>)
[$+01c]      00f00000 (addr <00f00000>)
[$+020]      ba49f40e
[$+024]      00000000
[$+028]      00000000
[$+02c]      00f00000 (addr <00f00000>)
[$+030]      00000000
[$+034]      00000000
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `Is32Bit`to your target execution context, set the `Size` and count of frames `FrameCount`, the base address (setting `NULL` as based address indicates that debuggee needs the current `rsp` register as the base address).

After allocating the below structure, you should also allocate as many frames structure (`DEBUGGER_SINGLE_CALLSTACK_FRAME`) that you want to read from the stack (`FrameCount * sizeof(DEBUGGER_SINGLE_CALLSTACK_FRAME)`).

```c
typedef struct _DEBUGGER_CALLSTACK_REQUEST
{
    BOOLEAN                           Is32Bit;
    UINT32                            KernelStatus;
    DEBUGGER_CALLSTACK_DISPLAY_METHOD DisplayMethod;
    UINT32                            Size;
    UINT32                            FrameCount;
    UINT64                            BaseAddress;
    UINT64                            BufferSize;

    //
    // Here is the size of stack frames
    //

} DEBUGGER_CALLSTACK_REQUEST, *PDEBUGGER_CALLSTACK_REQUEST;
```

The following structure shows the different fields of the frame structure.

```clike
typedef struct _DEBUGGER_SINGLE_CALLSTACK_FRAME
{
    BOOLEAN IsStackAddressValid;
    BOOLEAN IsValidAddress;
    BOOLEAN IsExecutable;
    UINT64  Value;
    BYTE    InstructionBytesOnRip[MAXIMUM_CALL_INSTR_SIZE];

} DEBUGGER_SINGLE_CALLSTACK_FRAME, *PDEBUGGER_SINGLE_CALLSTACK_FRAME;
```

The `DisplayMethod` can be selected from the below enum:

```clike
typedef enum _DEBUGGER_CALLSTACK_DISPLAY_METHOD
{
    DEBUGGER_CALLSTACK_DISPLAY_METHOD_WITHOUT_PARAMS,
    DEBUGGER_CALLSTACK_DISPLAY_METHOD_WITH_PARAMS,

} DEBUGGER_CALLSTACK_DISPLAY_METHOD;
```

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_MODE_CALLSTACK` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_CALLSTACK
```

In the returned structure, the `KernelStatus` and frames structures are filled by the kernel.

If the `KernelStatus` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending interpreting frames in the debugger.

```clike
VOID
CallstackShowFrames(PDEBUGGER_SINGLE_CALLSTACK_FRAME  CallstackFrames,
                    UINT32                            FrameCount,
                    DEBUGGER_CALLSTACK_DISPLAY_METHOD DisplayMethod,
                    BOOLEAN                           Is32Bit);
```

### Remarks

* If you don't specify the length, the default length for HyperDbg is `0x100` Bytes for 32-bit contexts and `0x200` for 64-bit contexts.
* HyperDbg automatically switches between 32-bit and 64-bit environments based on the debuggee's execution context.

{% hint style="warning" %}
Please note that you should specify a space between 'l' and the length in HyperDbg. For example, 'l100' is invalid, but 'l 100' is valid. (It's opposed to windbg).
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

None

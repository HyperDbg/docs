---
description: Description of '~' command in HyperDbg.
---

# ~ \(display and change the current operating core\)

### Command

> ~

### Syntax

> ~ \[core number \(hex value\)\]

### Description

Shows or changes the current operating core. This command can only be used in **Debugger Mode**.

### Parameters

**\[core number \(hex value\)\] \(optional\)**

          Target core number \(starts from zero\).

{% hint style="success" %}
If you don't specify any parameters to the '~' command, it shows the current operating core.
{% endhint %}

### Examples

The following command shows the current operating core.

```diff
HyperDbg> ~
current processor : 0x2
```

The following commands display and then change the current operating core to `0x1`.

```text
HyperDbg> ~
current processor : 0x2

HyperDbg> ~ 1
current operating core changed to 0x1
fffff801`633f157f    C3                                  ret

HyperDbg> ~
current processor : 0x1
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `NewCore` to your target core, and leave the `Result`.

```c
typedef struct _DEBUGGEE_CHANGE_CORE_PACKET {

UINT32 NewCore; 
UINT32 Result;

} DEBUGGEE_CHANGE_CORE_PACKET, *PDEBUGGEE_CHANGE_CORE_PACKET;
```

After that, send the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_MODE_CHANGE_CORE` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_CHANGING_CORE
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful, and you should wait for a new pause packet from debuggee. Otherwise, the returned result is an error \(e.g., the core number is invalid\), and the current operating core is not changed.

The following function is responsible for changing the core in the debugger.

```c
BOOLEAN KdSendSwitchCorePacketToDebuggee(UINT32 NewCore);
```

### **Remarks**

If the current core is equal to the target core, then it will not be changed.

### Requirements

None

### Related

None


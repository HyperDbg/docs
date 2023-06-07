---
description: Description of the 'event' command in HyperDbg.
---

# events (show and modify active/disabled events)

### Command

> events

### Syntax

> events
>
> events \[e|d|c all|EventNumber (hex)]
>
> events \[sc State (on|off)]

### Description

Shows a list of active/disabled events and commands or disables or clears the event(s). This command is also able to change the '[short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)' state of the corresponding execution of the event.

### Parameters

**\[e|d|c all|EventNumber (hex)] (optional)**

Type of action can be one of the `e`, `d`, or `c`.

**e** : enables the target event

**d** : disables the target event

**c** : clears and removes the target event

Specifies the target event (you can see the list of events and their unique event numbers by running the '**events**' command), you can specify `all` to apply your action to all active/disabled events.

{% hint style="success" %}
If you don't specify any parameters to the '**events**' command, it shows a list of events and their unique event numbers.
{% endhint %}

### Examples

The following command shows the list of active/disabled events.

```diff
HyperDbg> events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command disables an event with event number `1` and then we see the list of all events.

```diff
HyperDbg> events d 1

HyperDbg> events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command enables all of the events and commands.

```
HyperDbg> events e all

HyperDbg> events
0       (enabled)           !syscall 80
1       (enabled)           !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears an event with event number `1`.

```
HyperDbg> events c 1

HyperDbg> events
0       (enabled)           !syscall 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears and turns off every enabled and disabled event and commands.

```
0: kHyperDbg> events c all
```

The following function short-circuits the corresponding execution of the event. It **only** applies to the current execution of the event and will reset to the default short-circuiting state in the next execution of the event.&#x20;

```
0: kHyperDbg> events sc on
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_MODIFY_EVENTS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_MODIFY_EVENTS {

  UINT64 Tag;          // Tag of the target event that we want to modify
  UINT64 KernelStatus; // Kerenl put the status in this field
  DEBUGGER_MODIFY_EVENTS_TYPE
  TypeOfAction; // Determines what's the action (enable | disable | clear)

} DEBUGGER_MODIFY_EVENTS, *PDEBUGGER_MODIFY_EVENTS;
```

Where `Tag` is the tag of the event that you want to modify, you should leave `KernelStatus` as it will be filled by the kernel and shows whether the request was successful or not.

{% hint style="warning" %}
Keep in mind, `Tag` is not the same as event number, tags start from **DebuggerEventTagStartSeed** (by default **0x1000000**); thus, you can add this value to form a `Tag` from event number.
{% endhint %}

`TypeOfAction` shows what type of action you want the kernel to perform (**enable**, **disable, or clear**). It can be one of the following values.

```c
typedef enum _DEBUGGER_MODIFY_EVENTS_TYPE {
  DEBUGGER_MODIFY_EVENTS_ENABLE,
  DEBUGGER_MODIFY_EVENTS_DISABLE,
  DEBUGGER_MODIFY_EVENTS_CLEAR
} DEBUGGER_MODIFY_EVENTS_TYPE;
```

If you want to apply the action to all the events/commands, then you should fill the `Tag` with the following constant.

```c
#define DEBUGGER_MODIFY_EVENTS_APPLY_TO_ALL_TAG 0xffffffffffffffff
```

When the request is finished, the kernel fills the user-mode buffer with one of the following values, which indicates whether the request was successful or there was an error.

In the case of success :

```c
#define DEBUGEER_OPERATION_WAS_SUCCESSFULL 0xFFFFFFFF
```

In the case of error :

```c
#define DEBUGGER_ERROR_DEBUGGER_MODIFY_EVENTS_INVALID_TAG 0xc000000e
#define DEBUGGER_ERROR_DEBUGGER_MODIFY_EVENTS_INVALID_TYPE_OF_ACTION 0xc000000f
```

For event short-circuiting, you should send the following structure to the kernel.&#x20;

```clike
typedef struct _DEBUGGER_SHORT_CIRCUITING_EVENT
{
    UINT64  KernelStatus;      // Kerenl put the status in this field
    BOOLEAN IsShortCircuiting; // Determines whether to perform short circuting (on | off)

} DEBUGGER_SHORT_CIRCUITING_EVENT, *PDEBUGGER_SHORT_CIRCUITING_EVENT;
```

The above structure should be sent to the debugger by the following `RequestedAction`,

`DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_SET_SHORT_CIRCUITING_STATE`.

### Remarks

{% hint style="danger" %}
**Important note**

If you are operating in [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), you can enable or disable events while the debuggee is in a halt state. However, if you want to clear an event or all events then you lose the context as the debuggee is continued for some time to process the **clear** operation.
{% endhint %}

### Requirements

None

### Related

[Event short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)

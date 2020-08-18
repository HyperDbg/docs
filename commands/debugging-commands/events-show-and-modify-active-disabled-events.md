---
description: Description of 'event' command in HyperDbg.
---

# events \(show and modify active/disabled events\)

### Command

> events

### Syntax

> events \[e\|d\|c\] \[event number \(hex value\) \| all\]

### Description

Shows a list of active/disabled events and commands and also disables and clears the event\(s\).

### Parameters

\[e\|d\|c\] \(optional\)

          Type of action can be one of the `e`, `d`, or `c`.

        **e** : enables the target event

        **d** : disables the target event

        **c** : enables the target event

core \[event number \(hex value\) \| all\] \(optional\)

          Specifies the target event \(you can see the list of events and their unique numbers by running '**events**' command\), you can specify `all` to apply your action to all active/disabled events.

{% hint style="success" %}
If you don't specify any parameters to 'events' command then it shows a list of events and their unique numbers.
{% endhint %}

### Examples

The following command shows the list of active/disabled events.

```diff
HyperDbg >events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command disables an event with event number `1` and then we see the list of all events.

```diff
HyperDbg >event d 1

HyperDbg >events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command enables all of the events and commands.

```text
HyperDbg >event e all

HyperDbg >events
0       (enabled)           !syscall 80
1       (enabled)           !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears an event with event number `1`.

```text
HyperDbg >event c 1

HyperDbg >events
0       (enabled)           !syscall 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears and turns off every enabled and disabled event and commands.

```text
HyperDbg >event c 1
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
Keep in mind, `Tag` is not same as event number, tags start from **0x1000000**; thus, you can add this value to form a `Tag` from event number.
{% endhint %}

TypeOfAction show what type of action you want the kernel to perform for you \(**enable**, **disable, or clear**\). It can be one of the following values.

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

When the request is finished, the kernel fills the user-mode buffer with one of the following values which indicates whether the request was successful or there was an error.

In the case of success :

```c
#define DEBUGEER_OPERATION_WAS_SUCCESSFULL 0xFFFFFFFF
```

In the case of error :

```c
#define DEBUGGER_ERROR_DEBUGGER_MODIFY_EVENTS_INVALID_TAG 0xc000000e
#define DEBUGGER_ERROR_DEBUGGER_MODIFY_EVENTS_INVALID_TYPE_OF_ACTION 0xc000000f
```

### **Remarks**

None

### Requirements

None

### Related

None


---
description: How to programmatically activate an event using IOCTLs?
---

# IOCTL Requests for Events

In order to send an IOCTL and enable an event programmatically, you should fill the following instruction, this structure is used for tracing works in user mode and sending it to the kernel-mode, keep in mind that this structure is not what we save for events in kernel-mode.

After sending this structure to the kernel, if it's valid then the kernel creates a disabled event and is waiting for an action to be received then it activates the event.

```c
//
// Each command is like the following struct, it also used for tracing works in
// user mode and sending it to the kernl mode,
// THIS IS NOT WHAT WE SAVE FOR EVENTS IN KERNEL MODE
//
typedef struct _DEBUGGER_GENERAL_EVENT_DETAIL {

  LIST_ENTRY
  CommandsEventList; // Linked-list of commands list (used for tracing purpose
                     // in user mode)

  time_t CreationTime; // Date of creating this event

  UINT32 CoreId; // determines the core index to apply this event to, if it's
                 // 0xffffffff means that we have to apply it to all cores

  UINT32 ProcessId; // determines the process id to apply this to
                    // only that 0xffffffff means that we have to
                    // apply it to all processes

  BOOLEAN IsEnabled;

  UINT32 CountOfActions;

  UINT64 Tag; // is same as operation code
  DEBUGGER_EVENT_TYPE_ENUM EventType;

  UINT64 OptionalParam1;
  UINT64 OptionalParam2;
  UINT64 OptionalParam3;
  UINT64 OptionalParam4;

  PVOID CommandStringBuffer;

  UINT32 ConditionBufferSize;

} DEBUGGER_GENERAL_EVENT_DETAIL, *PDEBUGGER_GENERAL_EVENT_DETAIL;
```

Based on your request, you can select one of the following actions from`DEBUGGER_EVENT_TYPE_ENUM` enum. This enum will be updated in the future version but if you want to simulate a special command then check the command's manual to see what's the command's type.

```c

typedef enum _DEBUGGER_EVENT_TYPE_ENUM {

  HIDDEN_HOOK_READ_AND_WRITE,
  HIDDEN_HOOK_READ,
  HIDDEN_HOOK_WRITE,

  HIDDEN_HOOK_EXEC_DETOURS,
  HIDDEN_HOOK_EXEC_CC,

  SYSCALL_HOOK_EFER_SYSCALL,
  SYSCALL_HOOK_EFER_SYSRET,

  CPUID_INSTRUCTION_EXECUTION,

  RDMSR_INSTRUCTION_EXECUTION,
  WRMSR_INSTRUCTION_EXECUTION,

  IN_INSTRUCTION_EXECUTION,
  OUT_INSTRUCTION_EXECUTION,

  EXCEPTION_OCCURRED,
  EXTERNAL_INTERRUPT_OCCURRED,

  DEBUG_REGISTERS_ACCESSED,

  TSC_INSTRUCTION_EXECUTION,
  PMC_INSTRUCTION_EXECUTION

} DEBUGGER_EVENT_TYPE_ENUM;
```

If you want to use the debugger features, you should connect the `CommandsEventList` to the list of user-mode commands.

**OptionalParamX** is different in the case of each command, for example in **!epthook2** you should send the address of where you want to hook to the kernel as **OptionalParam1**, you have to check each command's manual to see what are its specific **OptionalParam**\(s\).

**Tag** is an ID that you can use later in action. 

**IsEnabled** has a user-mode usage to trace whether the event is enabled or not.

**CommandStringBuffer** is the string of the command you can ignore it.

If your event contains a condition buffer \(`ConditionBufferSize != 0`\), you can use set the size if `ConditionBufferSize` and append the buffer to the end of the above structure and when you send the buffer to the kernel, you should send the `sizeof(DEBUGGER_GENERAL_EVENT_DETAIL)+ ConditionBufferSize (if any)`.

Finally, you can send it to the kernel by using the following function.

```c
/**
 * @brief Register the event to the kernel
 */
BOOLEAN
SendEventToKernel(PDEBUGGER_GENERAL_EVENT_DETAIL Event,
                  UINT32 EventBufferLength);
```

 If you want to send it directly using IOCTL, you can use the following IOCTL :

```c
#define IOCTL_DEBUGGER_REGISTER_EVENT                                          \
  CTL_CODE(FILE_DEVICE_UNKNOWN, 0x806, METHOD_BUFFERED, FILE_ANY_ACCESS)
```

After sending the above event to the kernel, now you should chain an action or multiple actions to the event.

You should fill the following structure to send a "**Break**", "**Script**" and "**Custom Code**" t the kernel, for example, you can append the buffer of custom code after this structure and send them together to the kernel.

Also, **EventTag** is the unique ID that we sent previously in the event. 

```c
//
// Each event can have mulitple actions
// THIS STRUCTURE IS ONLY USED IN USER MODE
// WE USE SEPARATE STRUCTURE FOR ACTIONS IN
// KERNEL MODE
//
typedef struct _DEBUGGER_GENERAL_ACTION {
  UINT64 EventTag;
  DEBUGGER_EVENT_ACTION_TYPE_ENUM ActionType;
  UINT32 PreAllocatedBuffer;
  UINT32 CustomCodeBufferSize;

} DEBUGGER_GENERAL_ACTION, *PDEBUGGER_GENERAL_ACTION;
```

You can send the action to the kernel using the following function. Make sure to send the `sizeof(DEBUGGER_GENERAL_ACTION)+ Size of Custom code (if any)` to the following function.

```c
/**
 * @brief Register the action to the event
 */
BOOLEAN
RegisterActionToEvent(PDEBUGGER_GENERAL_ACTION Action,
                      UINT32 ActionsBufferLength);
```

If you want to register the action to the event directly using `DeviceIoControl`, you can use the following IOCTL.

```c
#define IOCTL_DEBUGGER_ADD_ACTION_TO_EVENT                                     \
  CTL_CODE(FILE_DEVICE_UNKNOWN, 0x807, METHOD_BUFFERED, FILE_ANY_ACCESS)
```


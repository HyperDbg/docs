---
description: What are events in HyperDbg & how to use them?
---

# Events

### What are events?

Events are an essential concept in HyperDbg. Almost all of the HyperDbg features are developed as an event.

For example, hidden hooks are an event. When this event is triggered, then your actions will be performed, or a syscall hook is an event, whenever the system executes an **SYSRET** instruction or a **SYSCALL** instruction, then the event is triggered.

{% hint style="success" %}
Note that some of the structures might be changed in the future \(means that some of the fields might be removed or new fields added to these structures\) versions of HyperDbg but these concepts remain the same.
{% endhint %}

Most of the basic debugger routines are implemented in **Debugger.c** in HyperDbg driver.

Generally, each event has a **condition** and might have zero or multiple **actions**. We'll describe conditions and actions in detail in later sections.

Now let's dig into the event internals.

### Event Structures and Design

In the first version of HyperDbg, structures are save into the kernel, like this :

```c
typedef struct _DEBUGGER_EVENT {
  UINT64 Tag;
  LIST_ENTRY EventsOfSameTypeList; // Linked-list of events of a same type
  DEBUGGER_EVENT_TYPE_ENUM EventType;
  BOOLEAN Enabled;
  UINT32 CoreId; // determines the core index to apply this event to, if it's
                 // 0xffffffff means that we have to apply it to all cores

  UINT32
      ProcessId; // determines the pid to apply this event to, if it's
                 // 0xffffffff means that we have to apply it to all processes

  LIST_ENTRY ActionsListHead; // Each entry is in DEBUGGER_EVENT_ACTION struct
  UINT32 CountOfActions;      // The total count of actions

  UINT64 OptionalParam1; // Optional parameter to be used differently by events
  UINT64 OptionalParam2; // Optional parameter to be used differently by events
  UINT64 OptionalParam3; // Optional parameter to be used differently by events
  UINT64 OptionalParam4; // Optional parameter to be used differently by events

  UINT32 ConditionsBufferSize;  // if null, means uncoditional
  PVOID ConditionBufferAddress; // Address of the condition buffer (most of the
                                // time at the end of this buffer)

} DEBUGGER_EVENT, *PDEBUGGER_EVENT;
```

`Tag` is a unique identity for each event, we want to send results back to the user mode, we use this `tag`. `Tag` is generated in the user mode application and will send to the kernel mode using different IOCTLs.

`EventsOfSameTypeList`, we save the events with the same type in a linked list \(more details later\).

`EventType`, shows the type of the current event.

`Enabled`, shows whether the current event is enabled or not. You can enable or disable an event using `Enabled`. When you disable an event, all the actions are disabled.

`CoreId`, shows the target core, if you specify a core number for this field then the event will only be executed into the specific core.

If you specify `DEBUGGER_EVENT_APPLY_TO_ALL_CORES = 0xffffffff` then the event will be executed in all cores.

`ProcessId`, shows the target process, if you specify a core number for this field then the event will only be executed into the specific process.

If you specify `DEBUGGER_EVENT_APPLY_TO_ALL_PROCESSES= 0xffffffff` then the event will be executed in all processes.

`ActionsListHead` hold a linked list of all the actions for this event and `CountOfActions` is the count of actions available in the `ActionsListHead`.

`OptionalParam1`, `OptionalParam2`, `OptionalParam3`, `OptionalParam4` are optional parameters that are event specific, for example for a hidden hook r/w `OptionalParam1` specifies the start physical address of page which is hooked and `OptionalParam2` specifies the end of hooked physical address, it is because EPT Violations happen for entire page not for a range. Other events have their special Optional Parameters too.

`ConditionBufferAddress`, holds the address of a buffer which will be executed to check the condition, the size of this buffer is available in `ConditionsBufferSize` . If the `ConditionsBufferSize` is null then it means that the event is unconditional.

### Creating a new event

In order to create a new event, you have to call the **DebuggerCreateEvent**.

Here's its prototype,

```c
PDEBUGGER_EVENT
DebuggerCreateEvent(BOOLEAN Enabled, UINT32 CoreId, UINT32 ProcessId, DEBUGGER_EVENT_TYPE_ENUM EventType, UINT64 Tag, UINT32 ConditionsBufferSize, PVOID ConditionBuffer);
```

For example we create a new event for a **`HIDDEN_HOOK_READ`** and as the condition buffer is null then it's unconditional.

```c
//
// Create event based on condition buffer
//
    
PDEBUGGER_EVENT Event1 = DebuggerCreateEvent(
        TRUE,
        DEBUGGER_EVENT_APPLY_TO_ALL_CORES,
        DEBUGGER_EVENT_APPLY_TO_ALL_PROCESSES,
        HIDDEN_HOOK_READ,
        0x85858585,
        0,
        NULL);
        
if (!Event1)
{
     LogError("Error in creating event");
}
```

{% hint style="danger" %}
Please note that **DebuggerCreateEvent**, should not be called in vmx-root mode.
{% endhint %}

### Registering an event

After creating an event, we should add action\(s\) to the event. `DebuggerAddActionToEvent`is used to add actions to the events \(we describe it in **actions** section\).

Finally, we have to use the following command to register the event.

```c
//
// Call to register
//
DebuggerRegisterEvent(Event1);
```

After registering an event, you have to initialize the debugging feature, for example for EFER syscall hook you need to configure `MSR_EFER` so the CPU generates the vm-exits and these vm-exits triggers the events.

Also, you don't need to initialize a feature if it's already initialized.

### Where we can find the events?

As I mentioned above, all logical cores have a single list of events. The `g_Events` which holds the details of all events.

```c
/**
 * @brief events list (for debugger)
 * 
 */
PDEBUGGER_CORE_EVENTS g_Events;
```

`DEBUGGER_CORE_EVENTS` contains linked-list for each event for example, `Events` contain the following lists in the first release.

```c
typedef struct _DEBUGGER_CORE_EVENTS
{
    //
    // Warnings : Only list entries should be in this list, nothing else
    //

    //
    // Do not add varialbe to this this list, just LIST_ENTRY is allowed
    //
    LIST_ENTRY HiddenHookReadAndWriteEventsHead;    // HIDDEN_HOOK_READ_AND_WRITE  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY HiddenHookReadEventsHead;            // HIDDEN_HOOK_READ  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY HiddenHookWriteEventsHead;           // HIDDEN_HOOK_WRITE  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY HiddenHooksExecDetourEventsHead;     // HIDDEN_HOOK_EXEC_DETOUR [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY HiddenHookExecCcEventsHead;          // HIDDEN_HOOK_EXEC_CC [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY SyscallHooksEferSyscallEventsHead;   // SYSCALL_HOOK_EFER_SYSCALL [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY SyscallHooksEferSysretEventsHead;    // SYSCALL_HOOK_EFER_SYSRET [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY CpuidInstructionExecutionEventsHead; // CPUID_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY RdmsrInstructionExecutionEventsHead; // RDMSR_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY WrmsrInstructionExecutionEventsHead; // WRMSR_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY InInstructionExecutionEventsHead;    // IN_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY OutInstructionExecutionEventsHead;   // OUT_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]
    LIST_ENTRY ExceptionOccurredEventsHead;         // EXCEPTION_OCCURRED [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents ]

} DEBUGGER_CORE_EVENTS, *PDEBUGGER_CORE_EVENTS;
```

When an event is triggered, it searches for the corresponding list in the above structure to find all related events.

If there is an event \(or multiple events\), it checks whether the event is enabled or not. If it was enabled then it checks to see if the event is conditional or unconditional. If it was conditional then it calls the condition function.

Condition buffer is a user-specific buffer \(code\), if it returns a zero value \(in `RAX`\) or in the other words, returns **FALSE**, then the event is not performed and HyperDbg check for other events \(If any\).

If the `RAX`is a non-zero value \(e.g. `RAX=0x1`\) then all the actions of that event will be performed.

### Triggering an event

Triggering an event is a general concept in HyperDbg, event will be triggered in the case of processor vm-exits, interrupts, callbacks, EPT violations and all the other things that indicate an event.

For example, we will trigger an event for **hidden hooks** and **memory monitor** when an EPT violation occurs.

Triggering event can be performed by calling the following routine.

```c
BOOLEAN
DebuggerTriggerEvents(DEBUGGER_EVENT_TYPE_ENUM EventType, PGUEST_REGS Regs, PVOID Context);
```

The caller should provide the kind of events to trigger \(`EventType`\) and pass the general purpose-registers \(`Regs`\), and an optional argument as `Context`. Most of the times the `Context` is the instruction pointer \(`RIP`\) or `GUEST_RIP`'s of when the event was triggered but it might be different in some events.

For example, the following code is used to trigger all the events related to `HIDDEN_HOOK_EXEC_DETOURS` events.

```c
    //
    // As the context to event trigger, we send the address of function
    // which is current hidden hook is triggered for it
    //
    DebuggerTriggerEvents(HIDDEN_HOOK_EXEC_DETOURS, Regs, CalledFrom);
```

This way, all of the events of the same type will be notified about the occurrence of the corresponding event.

### Finding an event

You can find the event using its `tag` by calling the following function.

```c
PDEBUGGER_EVENT
DebuggerGetEventByTag(UINT64 Tag)
```

### Disabling an event

In order to disable an event, you can call the following function using the `tag`.

```c
BOOLEAN
DebuggerDisableEvent(UINT64 Tag);
```

You can also, specify whether the event is enabled or disabled when you create the event using `DebuggerCreateEvent`.

### Enabling an event

In order to enable an event, you can call the following function using the `tag`.

```c
BOOLEAN
DebuggerEnableEvent(UINT64 Tag);
```

You can also, specify whether the event is enabled or disabled when you create the event using `DebuggerCreateEvent`.

### Removing an event

You can remove the event using the following function, this function will remove all the events with specified tag in the event list and free all the related pools.

```c
BOOLEAN
DebuggerRemoveEvent(UINT64 Tag)
```

If you remove an event, you are no longer able to disable or enable it.


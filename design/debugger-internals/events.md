---
description: What are events in HyperDbg & how to use them?
---

# Events

### What are the events?

Events are an essential concept in HyperDbg. Almost all of the HyperDbg features are developed as an event.

**Event** is the occurrence of an incident that is of interest to the debugger. This comprises a wide range of activities ranging from a specific system call (syscall) that the debugger is set to monitor, to access to a particular memory address. HyperDbg can be configured to perform arbitrarily defined actions upon the occurrence of each event.

For example, hidden hooks are an event. When this event is triggered, your actions will be performed, or a syscall hook is an event. Whenever the system executes an **SYSRET** instruction or a **SYSCALL** instruction, then the event is triggered.

{% hint style="success" %}
Note that some of the structures might be changed in the future (which means that some of the fields might be removed or new fields added to these structures) versions of HyperDbg, but these concepts remain the same.
{% endhint %}

Most of the basic debugger routines are implemented in **Debugger.c** in the HyperDbg driver.

Generally, each event has a **condition** and might have zero or multiple **actions**. We'll describe conditions and actions in detail in later sections.

Now let's dig into the event internals.

### Event Structures and Design

In the second version (v0.2.0.0) of HyperDbg, structures are saved into the kernel, like this :

```c
typedef struct _DEBUGGER_EVENT
{
    UINT64              Tag;
    LIST_ENTRY          EventsOfSameTypeList; // Linked-list of events of a same type
    VMM_EVENT_TYPE_ENUM EventType;
    BOOLEAN             Enabled;
    UINT32              CoreId; // determines the core index to apply this event to, if it's
                                // 0xffffffff means that we have to apply it to all cores

    UINT32
    ProcessId; // determines the pid to apply this event to, if it's
               // 0xffffffff means that we have to apply it to all processes

    LIST_ENTRY ActionsListHead; // Each entry is in DEBUGGER_EVENT_ACTION struct
    UINT32     CountOfActions;  // The total count of actions

    BOOLEAN EnableShortCircuiting; // indicates whether the short-circuiting event
                                   // is enabled or not for this event

    VMM_CALLBACK_EVENT_CALLING_STAGE_TYPE EventMode; // reveals the execution mode
    // of the event (whether it's a pre- or post- event)

    UINT64 OptionalParam1; // Optional parameter to be used differently by events
    UINT64 OptionalParam2; // Optional parameter to be used differently by events
    UINT64 OptionalParam3; // Optional parameter to be used differently by events
    UINT64 OptionalParam4; // Optional parameter to be used differently by events

    UINT32 ConditionsBufferSize;   // if null, means uncoditional
    PVOID  ConditionBufferAddress; // Address of the condition buffer (most of the
                                   // time at the end of this buffer)

} DEBUGGER_EVENT, *PDEBUGGER_EVENT;
```

`Tag` is a unique identity for each event, we want to send results back to the user mode, we use this `tag`. `Tag` is generated in the user-mode application and will send to the kernel-mode using different IOCTLs.

`EventsOfSameTypeList`, we save the events of the same type in a linked list (more details later).

`EventType` shows the type of the current event.

`Enabled` shows whether the current event is enabled or not. You can enable or disable an event using `Enabled`. When you disable an event, all the actions are disabled.

`CoreId` shows the target core. If you specify a core number for this field, then it will only be executed into the specific core.

If you specify `DEBUGGER_EVENT_APPLY_TO_ALL_CORES = 0xffffffff` then the event will be executed in all cores.

`ProcessId` shows the target process. If you specify a core number for this field, then the event will only be executed into the specific process.

If you specify `DEBUGGER_EVENT_APPLY_TO_ALL_PROCESSES= 0xffffffff` then the event will be executed in all processes.

`ActionsListHead` hold a linked list of all the actions for this event and `CountOfActions` is the count of actions available in the `ActionsListHead`.

`OptionalParam1`, `OptionalParam2`, `OptionalParam3`, `OptionalParam4` are optional parameters that are event specific. For example, a hidden hook r/w `OptionalParam1` specifies the hooked page's start physical address and `OptionalParam2` specifies the end of the hooked physical address. It is because EPT Violations happen for the entire page, not for a range. Other events have their special **Optional Parameters** too.

`ConditionBufferAddress` holds the address of a buffer, which will be executed to check the condition. The size of this buffer is available in `ConditionsBufferSize` . If the `ConditionsBufferSize` is null, then it means that the event is unconditional.

`EnableShortCircuiting` shows whether short-circuiting events are enabled for this event, or not. Short-circuiting event is a mechanism used to ignore the effects of some event, for example, emulation of **SYSCALL** instruction for this event will be ignored if this short-circuiting is enabled.

**EventMode** shows whether this event should be called before/after the emulation.

### Creating a new event

In order to create a new event, you have to call the **DebuggerCreateEvent**.

Here's its prototype,

```c
PDEBUGGER_EVENT
DebuggerCreateEvent(BOOLEAN Enabled, UINT32 CoreId, UINT32 ProcessId, DEBUGGER_EVENT_TYPE_ENUM EventType, UINT64 Tag, UINT32 ConditionsBufferSize, PVOID ConditionBuffer);
```

For example, we create a new event for a `HIDDEN_HOOK_READ`, and as the condition buffer is null; thus, it's unconditional.

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

After creating an event, we should add action(s) to the event. `DebuggerAddActionToEvent`is used to add actions to the events (we describe it in the **actions** section).

Finally, we have to use the following command to register the event.

```c
//
// Call to register
//
DebuggerRegisterEvent(Event1);
```

After registering an event, you have to initialize the debugging feature. For example, for the EFER syscall hook, you need to configure `MSR_EFER` so the CPU generates the vm-exits, and these vm-exits trigger the events.

Also, you don't need to initialize a feature if it's already initialized.

### Where can we find the events?

As I mentioned above, all logical cores have a single list of events. The `g_Events` which holds the details of all events.

```c
/**
 * @brief events list (for debugger)
 * 
 */
PDEBUGGER_CORE_EVENTS g_Events;
```

`DEBUGGER_CORE_EVENTS` contains a linked-list for each event. For example, `Events` contain the following lists in the first release.

```c
typedef struct _DEBUGGER_CORE_EVENTS
{
    //
    // Warnings : Only list entries should be in this list, nothing else
    //

    //
    // Do not add varialbe to this this list, just LIST_ENTRY is allowed
    //
    LIST_ENTRY HiddenHookReadAndWriteEventsHead;     // HIDDEN_HOOK_READ_AND_WRITE  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY HiddenHookReadEventsHead;             // HIDDEN_HOOK_READ  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY HiddenHookWriteEventsHead;            // HIDDEN_HOOK_WRITE  [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY EptHook2sExecDetourEventsHead;        // HIDDEN_HOOK_EXEC_DETOURS [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY EptHookExecCcEventsHead;              // HIDDEN_HOOK_EXEC_CC [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY SyscallHooksEferSyscallEventsHead;    // SYSCALL_HOOK_EFER_SYSCALL [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY SyscallHooksEferSysretEventsHead;     // SYSCALL_HOOK_EFER_SYSRET [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY CpuidInstructionExecutionEventsHead;  // CPUID_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY RdmsrInstructionExecutionEventsHead;  // RDMSR_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY WrmsrInstructionExecutionEventsHead;  // WRMSR_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY ExceptionOccurredEventsHead;          // EXCEPTION_OCCURRED [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY TscInstructionExecutionEventsHead;    // TSC_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY PmcInstructionExecutionEventsHead;    // PMC_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY InInstructionExecutionEventsHead;     // IN_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY OutInstructionExecutionEventsHead;    // OUT_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY DebugRegistersAccessedEventsHead;     // DEBUG_REGISTERS_ACCESSED [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY ExternalInterruptOccurredEventsHead;  // EXTERNAL_INTERRUPT_OCCURRED [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]
    LIST_ENTRY VmcallInstructionExecutionEventsHead; // VMCALL_INSTRUCTION_EXECUTION [WARNING : MAKE SURE TO INITIALIZE LIST HEAD , Add it to DebuggerRegisterEvent, Add it to DebuggerTriggerEvents, Add termination to DebuggerTerminateEvent ]

} DEBUGGER_CORE_EVENTS, *PDEBUGGER_CORE_EVENTS;
```

When an event is triggered, it searches for the corresponding list in the above structure to find all related events.

If there is an event (or multiple events), it checks whether the event is enabled or not. If it was enabled, then it checks to see if the event is conditional or unconditional. If it was conditional, then it calls the condition function.

Condition buffer is a user-specific buffer (code) if it returns a zero value (in `RAX`) or in other words, returns **FALSE**, then the event is not performed, and HyperDbg checks for other events (If any).

If the `RAX`is a non-zero value (e.g. `RAX=0x1`) then all the actions of that event will be performed.

### Triggering an event

Triggering an event is a general concept in HyperDbg. An event will be triggered in the case of processor a vm-exits, interrupts, callbacks, EPT violations, and all the other things that indicate an event.

For example, an event will trigger **hidden hooks** and **memory monitor** when an EPT violation occurs.

Triggering events can be performed by calling the following routine.

```c
BOOLEAN
DebuggerTriggerEvents(DEBUGGER_EVENT_TYPE_ENUM EventType, PGUEST_REGS Regs, PVOID Context);
```

The caller should provide the kind of events to trigger (`EventType`) and pass the general purpose-registers (`Regs`), and an optional argument as `Context`. Most of the times the `Context` is the instruction pointer (`RIP`) or `GUEST_RIP`'s of when the event was triggered, but it might be different in some events.

For example, the following code is used to trigger all the events related to `HIDDEN_HOOK_EXEC_DETOURS` events.

```c
    //
    // As the context to event trigger, we send the address of function
    // which is current hidden hook is triggered for it
    //
    DebuggerTriggerEvents(HIDDEN_HOOK_EXEC_DETOURS, Regs, CalledFrom);
```

This way, all of the same events will be notified about the corresponding event's occurrence.

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

You can specify whether the event is enabled or disabled when you create the event using `DebuggerCreateEvent`.

### Enabling an event

In order to enable an event, you can call the following function using the `tag`.

```c
BOOLEAN
DebuggerEnableEvent(UINT64 Tag);
```

You can specify whether the event is enabled or disabled when you create the event using `DebuggerCreateEvent`.

### Removing an event

You can remove the event using the following function. This function will remove all the events with the specified tag in the event list and free all the related pools.

```c
BOOLEAN
DebuggerRemoveEvent(UINT64 Tag);
```

If you remove an event, you are no longer able to disable or enable it.

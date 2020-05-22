---
description: What is actions in HyperDbg & how to use them?
---

# Actions

### What are actions?

Actions define the operation that the debugger should do, in the case of triggering an event. In the other words, events are container of actions.

Each event can have zero or many actions, in aد unconditional event, all actions are performed one by one with the insertion order and in conditional events, actions are performed only and only if the condition is met.

### Types of Actions

HyperDbg is not classic debugger, so we can have multiple kind of operations in the case of events.

Actions can be defined in 3 different types as demonstrated in the following enum.

```c
typedef enum _DEBUGGER_EVENT_ACTION_TYPE_ENUM {
  BREAK_TO_DEBUGGER,
  LOG_THE_STATES,
  RUN_CUSTOM_CODE

} DEBUGGER_EVENT_ACTION_TYPE_ENUM;
```

1. **Break to Debugger** : Exactly like other classic debuggers, this type of action halts the system and makes the system available for future user commands. This type of action is only available in debugging a remote machine, it is because you cannot halt your current local system.
2. **Log the Status** : This action type is a special feature that create a log from the registers, memory and special details \(pseudo-registers\) without halting the system and transfers the logs from kernel mode and vmx-root mode, safely to the debugger user mode. You can use this type of action in both debugging a remote machine and debugging a local machine.
3. **Run Custom Code** : Running a custom code is a special features that will allow you to execute your custom assembly codes in the case of triggering an event. This means that your assembly codes will be executed and the results will be returned safely to the debugger user mode. You can use this type of action in both debugging a remote machine and debugging a local machine.

### Break to Debugger

### Log the Status

### Run Custom Code

Running custom codes, gives you a fast and reliable way to execute your codes in the case of triggering events without breaking the whole system, so it's super fast.

This powerful feature can optionally give you a non-paged pool buffer with your specific size and gives the address of the buffer to you assembly code in `RCX`. You can safely use this buffer in you assembly code and if you want, HyperDbg will safely transfer this buffer to user mode for you.

First, you should create a buffer of bytes which performs your task. For example the following code is some `nops` that a custom buffer provides to the debugger. You can change it to what ever assembly bytes that you want without any limitation in size.

```c
    char CustomCodeBuffer[8];
    CustomCodeBuffer[0] = 0x90; //nop
    CustomCodeBuffer[1] = 0x90; //nop
    CustomCodeBuffer[2] = 0x90; //nop
    CustomCodeBuffer[3] = 0x90; //nop
    CustomCodeBuffer[4] = 0x90; //nop
    CustomCodeBuffer[5] = 0x90; //nop
    CustomCodeBuffer[6] = 0x90; //nop
    CustomCodeBuffer[7] = 0xc3; //ret
```

{% hint style="warning" %}
Don't forget to put a **0xc3** or **ret instruction** at the end of you custom code buffer, this way you give the program's execution back to the debugger and HyperDbg can continue normally, otherwise the HyperDbg won't get a chance to get back the execution and will cause a crash.
{% endhint %}

Now, you should fill the following structure which gives the details of your custom code to the debugger.

```c
typedef struct _DEBUGGER_EVENT_REQUEST_CUSTOM_CODE {
  UINT32 CustomCodeBufferSize;
  PVOID CustomCodeBufferAddress;
  UINT32 OptionalRequestedBufferSize;

} DEBUGGER_EVENT_REQUEST_CUSTOM_CODE, *PDEBUGGER_EVENT_REQUEST_CUSTOM_CODE;
```

For example the following code shows that we use the `CustomCodeBuffer` as the custom assembly code and also we set the size of the buffer. `OptionalRequestedBufferSize` is used to request a non-paged pool buffer, if this field is zero then it means that you don't need a non-paged buffer but if it's not zero, then HyperDbg will allocate a non-paged pool for you and will pass the address of the buffer each time as the `RCX` to you assembly code.

```c
    //
    // Add action for RUN_CUSTOM_CODE
    //
    
    DEBUGGER_EVENT_REQUEST_CUSTOM_CODE CustomCode = {0};

    CustomCode.CustomCodeBufferSize        = sizeof(CustomCodeBuffer);
    CustomCode.CustomCodeBufferAddress     = CustomCodeBuffer;
    CustomCode.OptionalRequestedBufferSize = 0x100;
```

Finally, you have to register the action to the event using `DebuggerAddActionToEvent`.

Here's the prototype of **DebuggerAddActionToEvent**.

```c
BOOLEAN
DebuggerAddActionToEvent(PDEBUGGER_EVENT Event, DEBUGGER_EVENT_ACTION_TYPE_ENUM ActionType, BOOLEAN SendTheResultsImmediately, PDEBUGGER_EVENT_REQUEST_CUSTOM_CODE InTheCaseOfCustomCode, PDEBUGGER_EVENT_ACTION_LOG_CONFIGURATION InTheCaseOfLogTheStates)
```

**Event** is the event which you want to register this action to it.

**ActionType** is the type of action \(described above\).

**SendTheResultsImmediately** this field shows whether the buffer should be sent immediately to the usermode or not.

It is because HyperDbg holds a queue of messages to be delivered to user mode and when the queue has multiple messages \(queue is full\), it sends all of them in an IRP packet to the user mode \(IRP Pending\), this makes the HyperDbg messaging more efficient as we're not going to send each messages separately in one IRP packet. 

If you set this field to `TRUE`, the buffer will be delivered to the user mode immediately, and if you set it to `FALSE`, then the buffers will be accumulated and delivered when the queue has multiple messages.

You should set it to `FALSE` in most cases but if you need immediate results the choose `TRUE` and it makes you computer substantially slower in high rates of data delivery but in low rates `TRUE` makes more scene.

**InTheCaseOfCustomCode** you should fill it as described above.

**InTheCaseOfLogTheStates** is used in log state, should be null in custom code.

The following example shows how to use the `DebuggerAddActionToEvent`.

```c
    DebuggerAddActionToEvent(Event1, RUN_CUSTOM_CODE, TRUE, &CustomCode, NULL);
```

{% hint style="danger" %}
Please note that **DebuggerAddActionToEvent** should not be called in vmx-root mode.
{% endhint %}

#### How to send buffers back to usermode?

If you didn't request a safe buffer then your assembly will be called in the following form.

```c
typedef PVOID DebuggerRunCustomCodeFunc(VOID);
```

If you request a safe non-paged pool buffer then your assembly will be called in the following form and as we're calling it with **fastcall** calling convention the you can expect buffer address in `RCX`. 

```c
typedef PVOID DebuggerRunCustomCodeWithPreAllocatedBufferFunc(PVOID PreAllocatedBufferAddress);
```

If and only if you request a buffer then you can return with the buffer address \(return with `RAX = Buffer address`\), HyperDbg checks for the address in `RAX`, if it's a valid address then it send it to the user mode otherwise it will be ignored.

You can also specify other buffers \(not the buffer that was safely passed to you function.\), HyperDbg will also send this buffer to the user mode with the size that you specified in request buffer.

For example, you request `0x100` bytes of non-paged pool, and HyperDbg passes this buffer to your function, now your returned a system buffer address in `RAX`, HyperDbg will send `0x100` bytes of System buffer to the user mode.

If you don't want any buffer in user mode then consider clearing the `RAX` before the return of your assembly code.


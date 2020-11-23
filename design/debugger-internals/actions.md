---
description: What is actions in HyperDbg & how to use them?
---

# Actions

### What are actions?

Actions define the operation that the debugger should do, in the case of triggering an event. In the other words, events are containers of actions.

Each event can have zero or many actions, in aد unconditional event, all actions are performed one by one with the insertion order and in conditional events, actions are performed only and only if the condition is met.

### Types of Actions

HyperDbg is not a classic debugger, so we can have multiple kinds of operations in the case of events.

Actions can be defined in 3 different types as demonstrated in the following enum.

```c
typedef enum _DEBUGGER_EVENT_ACTION_TYPE_ENUM {
  BREAK_TO_DEBUGGER,
  RUN_SCRIPT,
  RUN_CUSTOM_CODE

} DEBUGGER_EVENT_ACTION_TYPE_ENUM;
```

1. **Break** : Exactly like other classic debuggers, this type of action halts the system and makes the system available for future user commands. This type of action is only available in debugging a remote machine, it is because you cannot halt your current local system.
2. **Script** : This action type is a special feature that creates a log from the registers, memory and special details \(pseudo-registers\) without halting the system and transfers the logs from kernel mode and vmx-root mode, safely to the debugger user mode and also you can call predefined functions and change the state of the system directly. You can use this type of action in both debugging a remote machine and debugging a local machine.
3. **Custom Code** : Running a custom code is a special feature that will allow you to execute your custom assembly codes in the case of triggering an event. This means that your assembly codes will be executed and the results will be returned safely to the debugger user mode. You can use this type of action in both debugging a remote machine and debugging a local machine.

### Break

### Script

### Custom Code

Running custom codes gives you a fast and reliable way to execute your codes in the case of triggering events without breaking the whole system, so it's super fast.

This powerful feature can optionally give you a non-paged pool buffer with your specific size and gives the address of the buffer to your assembly code in `RCX`. You can safely use this buffer in your assembly code and if you want, HyperDbg will safely transfer this buffer to user mode for you.

First, you should create a buffer of bytes that performs your task. For example, the following code is some `nops` that a custom buffer provides to the debugger. You can change it to whatever assembly bytes that you want without any limitation in size.

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
Don't forget to put a **0xc3** or **ret instruction** at the end of you custom code buffer, this way you give the program's execution back to the debugger and HyperDbg can continue normally, otherwise, the HyperDbg won't get a chance to get back the execution and will cause a crash.
{% endhint %}

Now, you should fill the following structure which gives the details of your custom code to the debugger.

```c
typedef struct _DEBUGGER_EVENT_REQUEST_CUSTOM_CODE {
  UINT32 CustomCodeBufferSize;
  PVOID CustomCodeBufferAddress;
  UINT32 OptionalRequestedBufferSize;

} DEBUGGER_EVENT_REQUEST_CUSTOM_CODE, *PDEBUGGER_EVENT_REQUEST_CUSTOM_CODE;
```

For example, the following code shows that we use the `CustomCodeBuffer` as the custom assembly code and also we set the size of the buffer. `OptionalRequestedBufferSize` is used to request a non-paged pool buffer, if this field is zero then it means that you don't need a non-paged buffer but if it's not zero, then HyperDbg will allocate a non-paged pool for you and will pass the address of the buffer each time as the `RCX` to you assembly code.

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
DebuggerAddActionToEvent(PDEBUGGER_EVENT Event, DEBUGGER_EVENT_ACTION_TYPE_ENUM ActionType, BOOLEAN SendTheResultsImmediately, PDEBUGGER_EVENT_REQUEST_CUSTOM_CODE InTheCaseOfCustomCode, PDEBUGGER_EVENT_ACTION_RUN_SCRIPT_CONFIGURATION InTheCaseOfRunScript)
```

**Event** is the event which you want to register this action to it.

**ActionType** is the type of action \(described above\).

**SendTheResultsImmediately** this field shows whether the buffer should be sent immediately to the user-mode or not.

It is because HyperDbg holds a queue of messages to be delivered to user mode and when the queue has multiple messages \(the queue is full\), it sends all of them in an IRP packet to the user mode \(IRP Pending\), this makes the HyperDbg messaging more efficient as we're not going to send each message separately in one IRP packet. 

If you set this field to `TRUE`, the buffer will be delivered to the user -ode immediately, and if you set it to `FALSE`, then the buffers will be accumulated and delivered when the queue has multiple messages.

You should set it to `FALSE` in most cases but if you need immediate results the choose `TRUE` and it makes you computer substantially slower in high rates of data delivery but in low rates `TRUE` makes more scene.

**InTheCaseOfCustomCode** you should fill it as described above.

**InTheCaseOfRunScript** is used for script engine, should be null in custom code.

The following example shows how to use the `DebuggerAddActionToEvent`.

```c
    DebuggerAddActionToEvent(Event1, RUN_CUSTOM_CODE, TRUE, &CustomCode, NULL);
```

{% hint style="danger" %}
Please note that **DebuggerAddActionToEvent** should not be called in vmx-root mode.
{% endhint %}

#### How to send buffers back to user-mode?

If you didn't request a safe buffer or even request a safe buffer then your assembly will be called in the following form.

```c
typedef PVOID
DebuggerRunCustomCodeFunc(PVOID PreAllocatedBufferAddress, PGUEST_REGS Regs, PVOID Context);
```

If you request a safe non-paged pool buffer then your assembly will be called in the following form and as we're calling it with **fastcall** calling convention then you can expect buffer address in `RCX`.

```c
ReturnBufferToUsermodeAddress = Func(Action->RequestedBuffer.RequstBufferAddress, Regs, Context);
```

Otherwise, `RCX` is null \(in the case, you didn't need a safe buffer\).

```c
Func(NULL, Regs, Context);
```

In the above calls, `RDX` is the structure of guest's general-purpose registers, you can modify them directly and these registers will apply to the guest when it wants to continue its normal execution.

`R8` \(Context\) is an optional parameter that describes the state and it's different for each event, you have to check each event's documentation to see what it is in that event.

The following structure shows the state of registers in `Regs` parameter. You can modify or read the general-purpose registers based on this structure as a pointer to this structure is available in `RDX`.

```c
typedef struct _GUEST_REGS
{
    ULONG64 rax; // 0x00
    ULONG64 rcx;
    ULONG64 rdx; // 0x10
    ULONG64 rbx;
    ULONG64 rsp; // 0x20 
    ULONG64 rbp;
    ULONG64 rsi; // 0x30
    ULONG64 rdi;
    ULONG64 r8; // 0x40
    ULONG64 r9;
    ULONG64 r10; // 0x50
    ULONG64 r11;
    ULONG64 r12; // 0x60
    ULONG64 r13;
    ULONG64 r14; // 0x70
    ULONG64 r15;
} GUEST_REGS, *PGUEST_REGS;
```

{% hint style="success" %}
You can read other registers \(non-general purpose registers\) directly and modify them, we're not changing them or use them in debugger and hypervisor routines so reading and changing them will directly apply to the guests registers and will apply on the normal execution \(except XMMs\).
{% endhint %}


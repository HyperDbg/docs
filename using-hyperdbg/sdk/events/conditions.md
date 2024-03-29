# Conditions

### Creating a Condition

You can create a buffer that holds a few bytes that will be do anything you want. For example, you might want to check if the current thread is a special thread or check the privilege level of the requesting user or whatever you can do anything you want in your assembly code then at the end of the code, you can decide whether the actions of this event should be executed or not.

This is done by using `RAX`register. If you zero the `RAX`register, then return (`ret`), it means that you don't want the event actions to be executed (or in other words, you returned `FALSE`).

For example, you might decide to run `XOR RAX, RAX` at the end of your assembly code. This way, the event action will never be executed because RAX is zero, which means `FALSE`.

Or you can use `MOV RAX, 1` at the end of the code, the **RAX** is non-zero, which means that the event's actions should be executed.

By the way, it depends on your code and the condition of your code.

The following code shows how to create a condition buffer at the kernel.

```c
    //
    // Create condition buffer
    //
    char CondtionBuffer[8];
    CondtionBuffer[0] = 0x90; //nop
    CondtionBuffer[1] = 0x48; //xor rax, rax
    CondtionBuffer[2] = 0x31;
    CondtionBuffer[3] = 0xc0;
    CondtionBuffer[4] = 0x48; // inc rax
    CondtionBuffer[5] = 0xff;
    CondtionBuffer[6] = 0xc0;
    CondtionBuffer[7] = 0xc3; // ret
```

{% hint style="warning" %}
Don't forget to put a **0xc3** or **ret instruction** at the end of your condition buffer. This way, you give the program's execution back to the debugger, and HyperDbg can continue normally. Otherwise, the HyperDbg won't get a chance to get back the execution and cause a crash.
{% endhint %}

### Adding the condition to the event

Adding condition buffer to the event is done when you are creating the event using `DebuggerCreateEvent`, you have to specify both a pointer to the buffer and the size of the buffer.

Look at the following example,

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
        sizeof(CondtionBuffer),
        CondtionBuffer);
```

In the above example, `CondtionBuffer` is the pointer to the buffer that holds the assembly bytes and `sizeof(CondtionBuffer)` shows the size of the buffer.

{% hint style="info" %}
Each event can only have one condition.
{% endhint %}

The condition buffer function is called in the following form:

```c
typedef UINT64
DebuggerCheckForCondition(PGUEST_REGS Regs, PVOID Context);
```

The above function is called where `Regs` is in `RCX` and `Context` is in `RDX`.

`Regs` is the registers of the guest, you can directly modify them, and it will be applied to the guest in the normal execution, and you can also read these registers in this structure and the `Context` is event-specific, check each event's documentation to see what is in the `Context`.

The `Regs` or `RCX` is a pointer to the following structure.

```c
typedef struct _GUEST_REGS
{
    ULONG64 rax; // 0x00
    ULONG64 rcx; // 0x08
    ULONG64 rdx; // 0x10
    ULONG64 rbx; // 0x18
    ULONG64 rsp; // 0x20 
    ULONG64 rbp; // 0x28
    ULONG64 rsi; // 0x30
    ULONG64 rdi; // 0x38
    ULONG64 r8;  // 0x40
    ULONG64 r9;  // 0x48
    ULONG64 r10; // 0x50
    ULONG64 r11; // 0x58
    ULONG64 r12; // 0x60
    ULONG64 r13; // 0x68
    ULONG64 r14; // 0x70
    ULONG64 r15; // 0x78
} GUEST_REGS, *PGUEST_REGS;
```

{% hint style="success" %}
You can read other registers (non-general purpose registers) directly and modify them. We're not changing them or using them in debugger and hypervisor routines, so reading and changing them will directly apply to the guests' registers and apply to normal execution.
{% endhint %}

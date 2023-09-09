---
description: Description of 'event_inject' function in HyperDbg Scripts
---

# event\_inject

### Function

> event\_inject

### Syntax

> event\_inject( InterruptionType, Vector);

### Parameters

**\[ Expression (InterruptionType)]**

Type of the target interrupt. Can be selected from the following table:

<table><thead><tr><th width="217">Interruption Type</th><th>Description</th></tr></thead><tbody><tr><td>0</td><td>External interrupt</td></tr><tr><td>1</td><td>Reserved</td></tr><tr><td>2</td><td>Non-maskable interrupt (NMI)</td></tr><tr><td>3</td><td>Hardware exception (e.g,. #PF)</td></tr><tr><td>4</td><td>Software interrupt (INT n)</td></tr><tr><td>5</td><td>Privileged software exception (INT1)</td></tr><tr><td>6</td><td>Software exception (INT3 or INTO)</td></tr><tr><td>7</td><td>Other event</td></tr></tbody></table>

**\[ Expression (Vector)]**

Whether the short-circuiting should be enabled or disabled for the corresponding execution of the event.

| Name                          | Vector nr.        | Type       | Mnemonic | Error code? |
| ----------------------------- | ----------------- | ---------- | -------- | ----------- |
| Divide-by-zero Error          | 0 (0x0)           | Fault      | #DE      | No          |
| Debug                         | 1 (0x1)           | Fault/Trap | #DB      | No          |
| Non-maskable Interrupt        | 2 (0x2)           | Interrupt  | -        | No          |
| Breakpoint                    | 3 (0x3)           | Trap       | #BP      | No          |
| Overflow                      | 4 (0x4)           | Trap       | #OF      | No          |
| Bound Range Exceeded          | 5 (0x5)           | Fault      | #BR      | No          |
| Invalid Opcode                | 6 (0x6)           | Fault      | #UD      | No          |
| Device Not Available          | 7 (0x7)           | Fault      | #NM      | No          |
| Double Fault                  | 8 (0x8)           | Abort      | #DF      | Yes (Zero)  |
| Coprocessor Segment Overrun   | 9 (0x9)           | Fault      | -        | No          |
| Invalid TSS                   | 10 (0xA)          | Fault      | #TS      | Yes         |
| Segment Not Present           | 11 (0xB)          | Fault      | #NP      | Yes         |
| Stack-Segment Fault           | 12 (0xC)          | Fault      | #SS      | Yes         |
| General Protection Fault      | 13 (0xD)          | Fault      | #GP      | Yes         |
| Page Fault                    | 14 (0xE)          | Fault      | #PF      | Yes         |
| Reserved                      | 15 (0xF)          | -          | -        | No          |
| x87 Floating-Point Exception  | 16 (0x10)         | Fault      | #MF      | No          |
| Alignment Check               | 17 (0x11)         | Fault      | #AC      | Yes         |
| Machine Check                 | 18 (0x12)         | Abort      | #MC      | No          |
| SIMD Floating-Point Exception | 19 (0x13)         | Fault      | #XM/#XF  | No          |
| Virtualization Exception      | 20 (0x14)         | Fault      | #VE      | No          |
| Reserved                      | 21-29 (0x15-0x1D) | -          | -        | No          |
| Security Exception            | 30 (0x1E)         | -          | #SX      | Yes         |
| Reserved                      | 31 (0x1F)         | -          | -        | No          |
| Triple Fault                  | -                 | -          | -        | No          |
| FPU Error Interrupt           | IRQ 13            | Interrupt  | #FERR    | No          |

### Description

Enables or disables the '[event short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', only for the **corresponding** execution of the event.

### Examples

`event_sc(1);`

Enables short-circuiting for the event; thus, the **corresponding execution** of the event will be **ignored**.

`event_sc(0);`

Disables short-circuiting for the event; thus, the **corresponding execution** of the event **WON'T** be **ignored**.

### Remarks

{% hint style="info" %}
Please keep in mind, **disabling** means that the event will be **executed** while **enabling** means the event emulation will be **ignored** or **short-circuited**.
{% endhint %}

Starting from **v0.3**, this feature was added to the HyperDbg debugger.

### Related

[Event short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)

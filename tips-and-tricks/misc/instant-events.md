---
description: The instant event mechanism in HyperDbg
---

# Instant events

Starting from **v0.7** HyperDbg supports the "**instant event**" mechanism. This mechanism is mainly introduced to fix a fundamental problem of immediately applying events in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

Instant events addressed this issue by providing two types of events, **regular events**, and **big events**.

## What was the problem?

If you've previously used HyperDbg, you probably noticed that events (e.g., EPT hooks, syscall hooks, memory monitors, etc.) continue the debugger for a short time and then break the debugger again. Thus, the target process might find a chance to continue its execution and you'll lose the current context (registers, memory). This was a fundamental problem in HyperDbg and the new instant event mechanism solves this issue.

Starting from v0.7, HyperDbg guarantees to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution and the context is preserved.

## Regular Events



## Big Events



## Pools & Preallocations

## Safe Event Buffers

By default, instant events won't support event preallocated pools that are acccessible as $buffer but you can preallocate two types of buffers (big event-peallocation) and (regular event-preallocation) buffers.

### Regular Safe Event Buffers

### Big Safe Event Buffers



## Instant Events For EPT Hooks

### Memory Monitor Hooks

### EPT Hooks (Hidden Breakpoints)

### EPT Hooks (Detours Hooks)



## Changing Design Constants



## Instant Event Errors

There are multiple errors related to instant events. Here these errors are described as well as the situations where these errors happen in HyperDbg.&#x20;

#### 0xc0000040

This error states that in some situations like applying '!epthook' or '!monitor', specifying the target process id is not possible, because HyperDbg cannot find the structures of the target process and perform the context switching. You need to use the '.process' or the '.thread' command to switch to the target process memory layout, then apply your hooks.

#### 0xc0000041

This error states that the conditional buffer that you specified for the target event cannot be stored in the memory as there is not enough pre-allocated space for the target buffer. In order to fix that, you need to customize and rebuild HyperDbg. Please refer to the "**Changing Design Constants**" on this page.

#### 0xc0000042

This error happens regularly and it means that HyperDbg didn't allocate enough preallocated buffer to store your event. Usually, this error happens when you apply many events (for example many EPT hooks on different functions) without continuing the debuggee for some time.

In order to fix that, you have two options. You can either continue HyperDbg by running 'g' and after some time press CTRL+C and pause the debugger again, so HyperDbg finds a chance to reallocate new preallocated pools for your events.

If you don't want to continue HyperDbg, before applying events, you can use the 'prealloc' command to allocate as many preallocated pools as you need and after that, you can pause HyperDbg and apply your events.

For example, assume that I want to apply '!epthook' for 0x50 functions. If I apply all of the without continuing the debuggee, I encounter this error but if I run the following commands beforehand, I can apply the events without any problem.

```
0: kHyperDbg> prealloc regular-event 50
the requested pools are allocated and reserved

0: kHyperDbg> prealloc epthook 50
the requested pools are allocated and reserved
```

#### 0xc0000043

#### 0xc0000044

#### 0xc0000045

#### 0xc0000046

#### 0xc0000047

#### 0xc0000048

#### 0xc0000049

#### 0xc000004a

#### 0xc000004b

#### 0xc000004c


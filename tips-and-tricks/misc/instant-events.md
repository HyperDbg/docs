---
description: The instant event mechanism in HyperDbg
---

# Instant events

Starting from **v0.7** HyperDbg supports the "**instant event**" mechanism. This mechanism is mainly introduced to fix a fundamental problem of immediately applying events in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

Instant events addressed this issue by providing two types of events, **regular events**, and **big events**. Here we describe each of them in detail.

## What was the problem?

If you've previously used HyperDbg, you probably noticed that events (e.g., EPT hooks, syscall hooks, memory monitors, etc.) continue the debugger for a short time and then break the debugger again. Thus, the target process might find a chance to continue its execution and you'll lose the current context (registers, memory). This was a fundamental problem in HyperDbg and the new instant event mechanism solves this issue.

By employing instant events, now HyperDbg guarantees to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution and the context is preserved.

The implementation of this mechanism involves broadcasting events to the halted cores (in the VMX root-mode), and as you might know, allocating memory is not possible in the VMX-root mode as paging is not working. Because of that, HyperDbg is not able to allocate memory to store the details of each event in the debugger, and as a result, we use a couple of pre-allocated pools to use them to store the event details on the VMX root-mode.

Two types of events refer to the size of the buffer needed for each event.

## Regular Events

Regular events are those events that need a small number of bytes for the pre-allocated buffer and once you start HyperDbg, it allocates a tens of events. HyperDbg will store buffers (for [scripts](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) and event [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes)) with small sizes in these events.&#x20;

## Big Events

Big events are those events that need a bigger number of bytes (size). For example, the [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) buffer or the [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) buffer is bigger than normal events. In these cases, HyperDbg needs to allocate bigger buffers for the debuggee to use in the VMX-root mode. By default, HyperDbg won't allocate any buffers for Big Event, because these events are not regularly used, thus, to save memory HyperDbg won't allocate them.

## Enable or Disable Events

If you don't want to encounter these limitations, you can disable the instant event mechanism by using the following [constant](https://docs.hyperdbg.org/tips-and-tricks/misc/customize-build) and [compiling](https://docs.hyperdbg.org/getting-started/build-and-install#build-and-compile) HyperDbg. If you disable this mechanism, then the context (registers and memory) will be lost (changed) when you're applying events.

```c
/**
 * @brief Enable or disable the instant event mechanism
 * @details for more information: https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events
 *
 */
#define EnableInstantEventMechanism TRUE
```

## Clearing Events

As a result of the instant events, clearing and terminating events are also applied immediately. However, once you **clear** an event or all events, HyperDbg just disables the event(s). Immediately after you continue the debuggee, HyperDbg tries to process the **clear** operation and removes the effect of the event in the system.

Events are also able to call the '[event\_clear](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_clear)' function directly to remove other events or the current event.

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

```c
/**
 * @brief Default buffer count of packets for message tracing
 * @details number of packets storage for priority buffers
 */
#define MaximumPacketsCapacityPriority 50

```

## Instant Event Errors

HyperDbg cannot allocate an infinite amount of preallocated buffers and because of this limitation, there are multiple errors related to instant events. Here these errors are described as well as the situations where these errors happen in HyperDbg and how you can fix them.

{% hint style="warning" %}
Please note that the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command. So, you need to keep in mind that in case of errors, first run the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command and once you have enough buffers for your events, then apply all of your events instantly.

###
{% endhint %}

#### 0xc0000040

This error states that in some situations like applying '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' or '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)', specifying the target process id is not possible, because HyperDbg cannot find the structures of the target process and perform the context switching. You need to use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' or the '[.thread](https://docs.hyperdbg.org/commands/meta-commands/.thread)' command to switch to the target process memory layout, then apply your hooks.

#### 0xc0000041

This error states that the [conditional buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) that you specified for the target event cannot be stored in the memory as there is not enough pre-allocated space for the target buffer. In order to fix that, you need to customize and rebuild HyperDbg. Please refer to the "**Changing Design Constants**" on this page.

#### 0xc0000042

This error happens regularly and it means that HyperDbg didn't allocate enough preallocated buffer to store your event. Usually, this error happens when you apply many events (for example many EPT hooks on different functions) without continuing the debuggee for some time.

In order to fix that, you have two options. You can either continue HyperDbg by running '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' and after some time press CTRL+C and pause the debugger again, so HyperDbg finds a chance to reallocate new preallocated pools for your events.

If you don't want to continue HyperDbg, before applying events, you can use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command to allocate as many preallocated pools as you need and after that, you can pause HyperDbg and apply your events.

For example, assume that you want to apply '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' for 0x50 number of functions. If you apply all of them without continuing the debuggee, you'll encounter this error but if you run the following commands beforehand, events can be applied without any problem.

```
0: kHyperDbg> prealloc regular-event 50
the requested pools are allocated and reserved

0: kHyperDbg> prealloc epthook 50
the requested pools are allocated and reserved
```

#### 0xc0000043

This error states that the [conditional buffer](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) is big and cannot be stored in the pre-allocated pools. Usually, it happens if you are passing a long buffer of [conditions](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) to the event. In order to solve that, you can run the following command and HyperDbg will allocate a bigger buffer for your event and conditions.

```
0: kHyperDbg> prealloc big-event 5
the requested pools are allocated and reserved
```

The above command tells HyperDbg to reserve big pools for 5 events.

#### 0xc0000044

This error indicates that the system doesn't have enough resources (RAM) to allocate the target action in the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode).&#x20;

#### 0xc0000045

This error is the same as (**0xc0000042**) and it states HyperDbg didn't allocate enough preallocated buffer to store your action. Usually, this error happens when you apply many events (for example many EPT hooks on different functions) without continuing the debuggee for some time.

In order to fix that, you have two options. You can either continue HyperDbg by running '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' and after some time press CTRL+C and pause the debugger again, so HyperDbg finds a chance to reallocate new preallocated pools for your actions.

If you don't want to continue HyperDbg, before applying events, you can use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command to allocate as many preallocated pools as you need and after that, you can pause HyperDbg and apply your actions.

For example, assume that you want to apply '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' for 0x50 number of functions. If you apply all of them without continuing the debuggee, you'll encounter this error but if you run the following commands beforehand, events can be applied without any problem.

```
0: kHyperDbg> prealloc regular-event 50
the requested pools are allocated and reserved

0: kHyperDbg> prealloc epthook 50
the requested pools are allocated and reserved
```

#### 0xc0000046

This error is the same as (**0xc0000043**). It states that the [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) or the[ custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) buffer is big and cannot be stored in the pre-allocated pools. Usually, it happens if you are passing a long [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) or a long buffer of [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) assemblies to the event. In order to solve that, you can run the following command and HyperDbg will allocate a bigger buffer for your event and conditions.

```
0: kHyperDbg> prealloc big-event 5
the requested pools are allocated and reserved
```

The above command tells HyperDbg to reserve big pools for 5 events.

#### 0xc0000047

This error states that your [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) or the [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) buffer is so big that HyperDbg cannot store it. In order to fix that, you need to customize and rebuild HyperDbg. Please refer to the "**Changing Design Constants**" on this page.

#### 0xc0000048

This error states that you specified a pre-allocated buffer that is bigger than the stack of HyperDbg to send/receive it. You need to specify a small safe buffer or you need to customize and rebuild HyperDbg to allocate bigger stacks for the communication. This error happens on both the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode) and the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode).

#### 0xc0000049

By default, HyperDbg won't allocate [safe](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) requested buffers ([$buffer](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers)) for instant events. If you need a safe buffer, you need to tell HyperDbg before applying the event.

The following command allocates safe buffers for three events.

```
0: kHyperDbg> prealloc regular-safe-buffer 3
the requested pools are allocated and reserved
```

After running the above command, you can use the safe buffer ([$buffer](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers)) on your events.

#### 0xc000004a

This error means that your requested [safe](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) buffer is big and cannot fit into the preallocated buffers. In order to solve that, you can tell HyperDbg to allocate bigger safe buffers for you before applying the event by using the following command.

The following command allocates a big safe buffer for one event.

```
0: kHyperDbg> prealloc big-safe-buffer 1
the requested pools are allocated and reserved
```

#### 0xc000004b

This error states that you specified a very big [safe](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#pre-allocated-buffers) buffer and HyperDbg cannot store it in the memory.  In order to fix that, you need to customize and rebuild HyperDbg. Please refer to the "**Changing Design Constants**" on this page.

#### 0xc000004c

This error indicates that the system doesn't have enough resources (RAM) to allocate the safe buffers ([$buffer](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers)) in the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode).&#x20;

#### 0xc000004f

This error typically occurs when attempting to clear a substantial number of events while the debugger is paused in Debugger Mode, and the user hasn't resumed the debuggee for an extended period. When this error happens, the targeted event(s) become disabled but are not completely removed. This is due to HyperDbg's inability to clear them during the subsequent run, as the user-mode priority buffers are already filled to capacity. Since these unserviced events remained in the queue, HyperDbg cannot eliminate them. To resolve this issue, you can continue the debuggee and wait until all queued events are cleared (usually 2 to 10 seconds). Afterward, pause the debuggee once more and request the removal of new events.

If you want to extend this capacity, you can refer to the "**Changing Design Constants**" on this page.

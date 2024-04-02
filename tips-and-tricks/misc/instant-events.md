---
description: The instant event mechanism in HyperDbg
---

# Instant events

Starting from **v0.7** HyperDbg supports the "**instant event**" mechanism. This mechanism is mainly introduced to fix a fundamental problem of immediately applying events in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

Instant events addressed this issue by providing two types of events, **regular events**, and **big events**.&#x20;

Through the implementation of the instant event mechanism, our aim was to make the design complexities transparent to the user by preallocating buffers and considering some pre-defined limitations. Nevertheless, if you encounter errors or limitations, this documentation is here to assist you in resolving them. If clarification is needed, feel free to ask [questions](https://t.me/HyperDbg) or engage in [discussions](https://github.com/orgs/HyperDbg/discussions).

## What was the problem?

If you've previously used HyperDbg, you've probably noticed that events (e.g., EPT hooks, syscall hooks, memory monitors, etc.) continue the debugger for a short time and then break the debugger again. Thus, the target process might find a chance to continue its execution and you'll lose the current context (registers, memory). This was a fundamental problem in HyperDbg and the instant event mechanism solves this issue.

By employing instant events, now HyperDbg guarantees to keep debuggee in a halt state (in [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)); thus, nothing will change during its execution and the context is preserved.

The implementation of this mechanism involves broadcasting events to the halted cores (in the VMX root-mode), and as you might know, allocating memory is not possible in the VMX-root mode as paging is not working. Because of that, HyperDbg is not able to allocate memory to store the details of each event in the debugger, and as a result, we use a couple of pre-allocated pools to use them to store the event details on the VMX root-mode.

Two types of events refer to the size of the buffer needed for each event.

## Regular Events

Regular events are those events that need a small number of bytes for the pre-allocated buffer and once you start HyperDbg, it allocates a tens of events. HyperDbg will store buffers (for [scripts](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) and event [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes)) with small sizes in these events.&#x20;

## Big Events

Big events are those events that need a bigger number of bytes (size). For example, the [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) buffer or the [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) buffer is bigger than normal events. In these cases, HyperDbg needs to allocate bigger buffers for the debuggee to use in the VMX-root mode. By default, HyperDbg won't allocate any buffers for big events, because these events are not regularly used, thus, to save memory HyperDbg won't allocate them.

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

Events are also able to call the '[event\_clear](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_clear)' function directly from the scripts to remove other events or even the current event.

## Pools & Preallocations

As explained earlier, it's not possible to allocate buffers in the VMX-root mode. HyperDbg tries to allocate buffers whenever possible but if you're applying a large number of events all of them without continuing the debuggee, the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command is designed to make HyperDbg able to support these scenarios.

Note that, the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command will continue the debuggee for some time (in [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)). This means that you lose the current context (registers & memory) after executing this command. So, you need to use this command before applying events.

### Event Regular Buffers

By default, HyperDbg allocates a couple of buffers for storing events. If you continue the debugger after applying events, then HyperDbg re-allocates more buffers to replace previously used buffers but if you want to apply many events instantly, you need to tell HyperDbg beforehand to allocate more buffers for your events.&#x20;

The following command is used to tell HyperDbg to allocate buffers to store **regular** events.&#x20;

```
 prealloc regular-event 10
```

In the above example, HyperDbg preallocates `10` buffers for **10** regular events.

### Event Big Buffers

If you have a very long [script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) or [custom code](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes) buffer, then you can use the **big** events instead.

```
prealloc big-event 12
```

In the above example, HyperDbg preallocates `12` buffers for **12** big events. Usually, you need to use the regular events and if HyperDbg shows an error and indicates that the size of the buffer is too big and it's not able to store the action buffer, then you can use a big event.

### Safe Event Buffers

By default, instant events won't support preallocated safe buffers that are accessible as `$buffer` [pseudo-registers](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#pseudo-registers) but you can preallocate two types of buffers (**big event-peallocation**) and (**regular event-preallocation**) buffers.

### Regular Safe Event Buffers

If you need a small buffer (explained later in the [Changing Design Constants](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events#changing-design-constants) section), then you can use the following command. You can also specify the number of events that you need a regular safe buffer for them.

```
prealloc regular-safe-buffer 10
```

After that, you'll be able to use the events with the `$buffer` parameter.

```
!epthook nt!ExAllocatePoolWithTag buffer 100 script { ... }
```

### Big Safe Event Buffers

If you need a bigger buffer (explained later in the [Changing Design Constants](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events#changing-design-constants) section), then you can use the following command. You can also specify the number of events that you need a big safe buffer for them.

```
prealloc big-safe-buffer 10
```

After that, you'll be able to use the events with the `$buffer` parameter. (Note the size of the buffer is bigger than the regular events)

```
!epthook nt!ExAllocatePoolWithTag buffer 2000 script { ... }
```

## Instant Events For EPT Hooks

EPT hooks need extra pre-allocated buffers. If you want to use EPT hook events ([!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook), [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2), and [!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)), then you need to preallocate buffers for them.

By default, if you use EPT hooks and continue the debuggee after applying events, HyperDbg tries to re-allocate more buffers for the next EPT hooks but if you want to apply all of them without continuing the debuggee, then you need to use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command.

Please note that EPT hooks are events, so first you need to preallocate event buffers (explained [above](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events#event-regular-buffers)) and EPT hook buffers.

### Memory Monitor Hooks

If you want to use [memory monitor hooks](https://docs.hyperdbg.org/design/features/vmm-module/design-of-monitor), you can preallocate buffers for them. For example, assume that we want to apply fifteen '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' events, first, we allocate `15` events buffer.

```
 prealloc regular-event 15
```

Then, we preallocate buffers for this specific EPT monitor event.

```
prealloc monitor 15
```

### EPT Hooks (Hidden Breakpoints)

If you want to use [classic hidden hooks](https://docs.hyperdbg.org/design/features/vmm-module/design-of-epthook), you can preallocate buffers for them. For example, assume that we want to apply fifteen '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' events, first, we allocate `15` events buffer.

```
 prealloc regular-event 15
```

Then, we preallocate buffers for this specific EPT hook event.

```
prealloc epthook 15
```

### EPT Hooks (Detours Hooks)

If you want to use [detours EPT hooks](https://docs.hyperdbg.org/design/features/vmm-module/design-of-epthook2), you can preallocate buffers for them. For example, assume that we want to apply fifteen '[!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2)' events, first, we allocate `15` events buffer.

```
 prealloc regular-event 15
```

Then, we preallocate buffers for this specific event EPT hook event.

```
prealloc epthook2 15
```

## Changing Design Constants

Here are a couple of design constants and their descriptions. If you want to customize HyperDbg (and compile it), you can change the following constants.

The following constant shows the maximum number of packets that can be delivered to the user-mode. For example, if you want to clear tens of events and HyperDbg shows an error message indicating that the user-mode priority buffers are full, you can increase the following constant to fix the problem.

```c
#define MaximumPacketsCapacityPriority 50
```

The following constant shows the maximum number of EPT hooks that HyperDbg allocates pre-allocated buffers for them. If you want to apply more EPT hooks without continuing the debuggee, you can increase the following constant.

```c
#define MAXIMUM_NUMBER_OF_INITIAL_PREALLOCATED_EPT_HOOKS 5
```

The following constant shows the maximum number of **regular** events that you can apply immediately without continuing the debuggee. If you want to apply tens of events without using the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command, you can increase the value of this constant.

```c
#define MAXIMUM_REGULAR_INSTANT_EVENTS 20
```

The following constant shows the maximum number of **big** events that you can apply immediately without continuing the debuggee. If you want to apply tens of events without using the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command, you can increase the value of this constant.

```c
#define MAXIMUM_BIG_INSTANT_EVENTS 0
```

The following constant shows the maximum number of bytes to store the details of a **regular** event. If you're using a very big [condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) buffer, you can increase this constant.

```c
#define REGULAR_INSTANT_EVENT_CONDITIONAL_BUFFER sizeof(DEBUGGER_EVENT) + 100
```

The following constant shows the maximum number of bytes to store the details of a **big** event. If you're using a very big [condition](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-a-condition) buffer, you can increase this constant.

```c
#define BIG_INSTANT_EVENT_CONDITIONAL_BUFFER sizeof(DEBUGGER_EVENT) + PAGE_SIZE 
```

The following constant shows the maximum number of bytes that an action ([script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) and [custom codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes)) can be for the **regular** event. If you want to apply bigger scripts or custom codes, you can increase this constant.

```c
#define REGULAR_INSTANT_EVENT_ACTION_BUFFER sizeof(DEBUGGER_EVENT_ACTION) + (PAGE_SIZE * 2)
```

The following constant shows the maximum number of bytes that an action ([script](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#script) and [custom codes](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/how-to-create-an-action#custom-codes)) can be for the **big** event. If you want to apply bigger scripts or custom codes, you can increase this constant.

```c
#define BIG_INSTANT_EVENT_ACTION_BUFFER sizeof(DEBUGGER_EVENT_ACTION) + MaxSerialPacketSize
```

The following constant shows the maximum number of bytes allocated for the safe pre-allocated buffer of the **regular** events. This is mainly used if you pass the `buffer` parameter to events. By default, HyperDbg won't allocate safe buffers and you need to use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command to allocate them. You can increase the size of these buffers by changing this constant.

```c
#define REGULAR_INSTANT_EVENT_REQUESTED_SAFE_BUFFER PAGE_SIZE
```

The following constant shows the maximum number of bytes allocated for the safe pre-allocated buffer of the **regular** events. This is mainly used if you pass the `buffer` parameter to events. By default, HyperDbg won't allocate safe buffers and you need to use the '[prealloc](https://docs.hyperdbg.org/commands/debugging-commands/prealloc)' command to allocate them. You can increase the size of these buffers by changing this constant.

```c
#define BIG_INSTANT_EVENT_REQUESTED_SAFE_BUFFER MaxSerialPacketSize
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

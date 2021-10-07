---
description: Description of 'pause' function in HyperDbg Scripts
---

# pause

## Function

> pause

## Syntax

> pause\( \);

## Parameters

No argument.

## Description

Breaks and halts the system and give control to the kernel debugger.

## Examples

`pause();`

Break and halt the system and give control to the debugger.

## **Remarks**

{% hint style="warning" %}
You can **only** use this function in **Debugger Mode**, and you **cannot** use it on VMI **Mode or local debugging**.

This command must be used in [events](https://docs.hyperdbg.org/design/debugger-internals/events). You should avoid using this function with the '[?](https://docs.hyperdbg.org/commands/debugging-commands/eval)' command. Otherwise, the system will halt.
{% endhint %}

## Related

None


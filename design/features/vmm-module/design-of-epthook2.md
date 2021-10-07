---
description: Design of !epthook2 command
---

# Design of !epthook2

The implementation of the [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2) command is derived from our [Hypervisor From Scratch \(Part 8\)](https://rayanfam.com/topics/hypervisor-from-scratch-part-8/). 

### Super fast, in-line hidden hooks

For execution hooks, we use a capability in Intel processors called **execute-only**.

**Execute-only** means that we can have a page with **execute access** enabled while **read and write access** is disabled.

If the user wants an execution hook, we find the entry in the EPT Table and unset read and write access and set the execute access. Then we create a copy from the original page \(**Page A**\) to somewhere else \(**Page B**\) and modify the copied page \(**Page B**\) with an absolute jump to the hook function.

Each time any instruction attempted to execute our function, the absolute jump is performed, and our hook function is called. Each time any instruction tries to read or write to that location, an EPT Violation occurs as we unset **read and write access** to that page, so we can swap the original page \(Page A\) and set the monitor trap flag to restore the hook after executing one instruction.

**Implementing Hidden Hooks**

For hooking functions, first, we split the page into 4KB entries, as described in the [previous part](https://rayanfam.com/topics/hypervisor-from-scratch-part-7/). Then find the entry and read that entry. We want to save the details of a hooked page so we can use it later. For reading/writing hooks, we unset read or write or both, while for executing hooks, we unset read/write access and set execute access and also copy the page contents into a new page and swap the entry’s physical address with the second page’s physical address \(fake page’s physical address\).

Then we build a trampoline \(explained later\) and finally decide how to invalidate the TLB based on vmx-state \(vmx-root or vmx non-root\) and finally add the hook details to the **HookedPagesList**.

Now we need a function that creates another page and patches the original page \(**Page A**\) with an absolute jump \(trampoline\) that jumps another page \(**Page B**\).

On \(**Page B**\), we will jump to the hooked function, and also this function copies the bytes that are patched to the \(**Page B**\) and save the original function for the caller to return back to the original page on \(Page B\).

This is a simple inline hook that we use our Length Disassembler Engine and build our detours function.

In the case of EPT Violations, first, we find the details of the physical address that caused this vm-exit. Then we call **EptHandleHookedPage** to create a log about the details. Then we set an MTF to restore to the hooked state after executing one instruction.

Each time an EPT Violation occurs, we check whether it was because of a **Read Access** or a **Write Access** or an **Execute Access** violation and log **GUEST\_RIP**, then we restore the initial flags \(All read, write, and exec is allowed\).  
  
  



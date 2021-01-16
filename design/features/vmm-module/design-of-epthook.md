---
description: Design of !epthook command
---

# Design of !epthook

The most interesting feature in classic hypervisors is EPT hidden hooks.

In HyperDbg, we designed two types of hidden EPT hooks. First, a classic EPT hook that tries to hide breakpoints using EPT \([**!epthook**](https://docs.hyperdbg.com/commands/extension-commands/epthook)\), and the second EPT hook is hidden detours \([**!epthook2**](https://docs.hyperdbg.com/commands/extension-commands/epthook2)\).

The classic EPT Hook uses the Execute Only feature in Intel Extended Page Table \(EPT\).

Generally, [!epthook2](https://docs.hyperdbg.com/commands/extension-commands/epthook2) is much faster than the [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) because it's like hidden inline hooks, but it has some limitations described [here](https://docs.hyperdbg.com/commands/extension-commands/epthook2#remarks), but the classic EPT hook can be used in both user-mode and kernel-mode without limitation.

This hook copies all the target page contents \(containing the breakpoint address\(es\)\) to a new location then puts the breakpoint on the new page.

After that, it unsets the **Read** and **Write** bits of that page but sets the **Execute** bit and change the physical address of that entry to the new location.

Now, if someone tries to **Read** or **Write** on that virtual address, then **EPT Violation** occurs, and we have a chance to bring the original physical address back to the page entry and set both **Read** and **Write** bits of that page but unset the **Execute** bit, so the page is not executable, we also set VMCS's **M**onitor **T**rap **F**lag \(a.k.a. _**MTF**_\) and thus after a read or write \(which causes the EPT Violation\), we change everything back to the executable but not readable and writable page.

This way, everything that tries to read or write on that page can conclude that it's not modified, and meanwhile, we have our breakpoints there.

Whenever breakpoint triggers, we set the _**Exception Bitmap**_ of breakpoint to cause vm-exit. If the vm-exit was because of our breakpoint, then we pass it to the debugger; otherwise, we inject a **\#BP** back to the guest. Using this way, we're sure that even the breakpoint itself is transparent from the Operating System and the target application.

You should keep in mind that exactly like classic debugger's breakpoints, you have to put breakpoints \(0xcc\) to the addresses that are aligned by instruction length, or in other words, you should put a breakpoint on the first byte of the instructions; otherwise, it changes the instruction to a new instruction and causes errors.

This feature works on vmx root-mode.


---
description: What is "unsafe" behavior in using HyperDbg
---

# The "unsafe" behavior

### What do we mean by "safe" and "unsafe"?

The term “safe” and “unsafe” is used a lot in HyperDbg. By “safe,” we mean something that works all the time and won’t cause a system crash or system halt.

It’s so tricky to manage code in the VMX root-mode. As HyperDbg gives you the ability to run your custom assembly code in all modes of execution, you should avoid doing some “**unsafe**” behavior that leads to system instability.

It is because, in the VMX root-mode, interrupts are masked (disabled), or transfer buffer from the VMX root-mode to the VMX non-root mode needs extra effort, and we should be cautious and avoid executing some APIs to be safe.

HyperDbg provides you a way of safely accessing a non-paged pool in user, kernel, and VMX root, and it sends the buffer to the user mode and HyperDbg itself in a safe manner.

### Things we should never do

* You should never use an NT API in the VMX-root if it has IRQL limitations. Make sure that you use only those APIs that are marked with (**ANY LEVEL**) in MSDN. For example, calling **ExAllocatePoolTag** will halt the system most of the time. Also, sometimes it's observed that even "**ANY LEVEL**" functions led to a system fault.
* You should not access a paged pool while you're executing in the VMX root-mode. If you access these pages and the page is paged out, it'll result in a system halt or crash. It is because interrupts and exceptions are masked off in the VMX-root mode, and a page-fault exception will never deliver.
* When you're executing in the VMX-root, your **cr3** view of memory is the **SYSTEM** process (**pid=4**)'s view of memory. If you want to access another process memory, you should change the **cr3** by yourself and return to system **cr3** immediately. Otherwise, you won't let the HyperDbg return gracefully, and you'll see BSOD.
* Do not directly access user mode codes from the VMX-root mode. It is because user-mode codes are located on paged memory, and there are two problems here. First, you need to find the **cr3** of the user, and it's not the process **cr3** because, for **KPTI** mitigation of **Meltdown**, **cr3** of user space is different from kernel **cr3**. It's also observed that accessing a user-mode memory (even if it's recently executed) causes a system crash. The problem is solved if you inject a **#PF** to the guest with that address. Hence, it is maybe because Intel has different caches (instruction & data) for its internal use, and the page-fault for instruction and data is different, so executing an instruction doesn't mean that it's paged-in for data (read/write) access.

### How can we access the memory?

* We can access the memory safely by using the functions provided by HyperDbg commands and HyperDbg's script engine. For example, if we want to read memory from the current process, we can use the regular [db, dc, dd, dq (read virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/d), and modify the memory using the [eb, ed, eq (edit virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/e) commands. On the other hand, in the script engine, you can use different memory reading [keywords](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#keywords) to read the memory or use the [eb, ed, eq](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/eb-ed-eq) functions to modify the memory.
* Other than that, you can use the [check\_address](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/check_address) function to make sure whether an address is valid and safe to access or not.

### The address is valid, but HyperDbg can't read it!

* Sometimes, addresses that you try to access are valid but HyperDbg is not able to read or modify the address and keeps showing a message that the address is invalid. This is mainly because the address is paged out and is not available in RAM. Or in some cases, the address is actually available in the RAM but the operating system didn't make it present for the current process's memory layout. The second case happens mostly when you start a new process.
* In order to solve that, we could use the '[.pagein](https://docs.hyperdbg.org/commands/meta-commands/.pagein)' command to bring the pages into the RAM and make it available for both the process and HyperDbg.

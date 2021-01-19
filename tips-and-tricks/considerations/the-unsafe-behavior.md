---
description: What is "unsafe" behavior in using HyperDbg
---

# The "unsafe" behavior

### What do we mean by "safe" and "unsafe"?

The term “safe” and “safe” is used a lot in HyperDbg. By “safe,” we mean something that works all the time and won’t cause a system crash or system halt. 

It’s because it’s so tricky to manage codes in vmx root-mode. As HyperDbg gives you the ability to run your custom assembly codes in all modes of execution, you should avoid doing some “unsafe” behavior that leads to system instability.

It is because, in the vmx-root mode, interrupts are masked \(disabled\), or transfer buffer from vmx root-mode to vmx non-root mode needs extra effort, and we should be cautious and avoid executing some APIs to be safe.

HyperDbg provides you a way of safely accessing a non-paged pool in user, kernel, and vmx-root, and it sends the buffer to the user mode and HyperDbg itself in a safe manner. 

### Things we should never do

* You should never use an NT API in vmx root if it has IRQL limitations. Make sure that you use only those APIs that are marked with \(**ANY LEVEL**\) in MSDN. For example, calling **ExAllocatePoolTag** will halt the system most of the time. Also, sometimes it's observed that even "**ANY LEVEL**" functions led to a system-fault.



* You should not access a paged-pool while you're executing in vmx root. If you access these pages and the page is paged-out, it'll result in a system-halt or crash. It is because interrupts and exceptions are masked off in the vmx-root mode, and a page-fault exception will never deliver.



*  When you're executing in vmx root, your **cr3** view of memory is the **SYSTEM** process \(**pid=4**\)'s view of memory. If you want to access another process memory, you should change the **cr3** by your self and return to system **cr3** immediately. Otherwise, you won't let the HyperDbg return gracefully, and you'll see BSOD.



* Do not directly access user mode codes from the vmx-root mode. It is because user-mode codes are located on paged memory, and there are two problems here. First, you need to find the **cr3** of the user, and it's not the process **cr3** because, for **KPTI** mitigation of **Meltdown**, **cr3** of user space is different from kernel **cr3**. It's also observed that accessing a user-mode memory \(even if it's recently executed\) causes a system crash. The problem is solved if you inject a \#PF to the guest with that address. Hence, it is maybe because Intel has different caches \(instruction & data\) for its internal use, and the page-fault for instruction and data is different, so executing an instruction doesn't mean that it's paged-in for data \(read/write\) access.


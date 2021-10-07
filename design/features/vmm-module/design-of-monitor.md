---
description: Design of !monitor command
---

# Design of !monitor

The implementation of the [!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor) command is derived from our [Hypervisor From Scratch \(Part 8\)](https://rayanfam.com/topics/hypervisor-from-scratch-part-8/). 

### **Simulating Hardware Debug Registers Without Any Limitation**

Have you ever used hardware debugger registers ?!

The debug registers allow researchers and programmers to selectively enable various debug conditions \(read, write, execute\) associated with a set of four debug addresses without any change in program instructions.

As you know, we can set up to 4 locations for these hardware registers, and it’s the worst limitation for these registers.

What if we have a structure \(let say **\_EPROCESS**\) and want to see what function in Windows **Read** or **Write** in this structure?

It’s not possible with current debug registers, but we use EPT to rescue!

### **Hidden Hooks Scenarios for Read/Write and Execute**

We have two strategies for hidden hooks, one for **Read/Write** and one for **Execute**.

We unset read or write or both \(based on how the user wants\) in the entry corresponding to the address.

This means before the operating system or a kernel driver tries to read or write, a vm-exit occurs, and an EPT Violation will notify us. In the EPT Violation handler, we log the address that tries to read or write, then we find the entry in the EPT table and set both read and write \(means that any read or write to the page is allowed\) and set an MTF flag.

VMM resumes, and one instruction executes, or in other words, read or write is performed, then an MTF vm-exit occurs. In the MTF vm-exit handler, we unset the read and write access again, so any future access to that page will cause an EPT Violation.

Note that all of the above scenarios happen to one core. Each core has a separate TLB and separate Monitor Trap Flag.  



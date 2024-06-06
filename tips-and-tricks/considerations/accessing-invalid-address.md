---
description: Considerations for accessing memory in different modes
---

# Accessing Invalid Address

Accessing memory could be challenging depending on the fact that HyperDbg tries to access memory from VMX-root mode. Generally, if you see the following error in HyperDbg, this page is to help you fix this problem.

<pre><code>err, invalid address (c0000005)
<strong>address may be paged-out or unavailable on the page table due to 'demand paging'
</strong>please refer to https://docs.hyperdbg.org/tips-and-tricks/considerations/accessing-invalid-address for further information
</code></pre>

As the code implies, HyperDbg couldn't determine whether the address is valid or invalid. It typically happens when the address may be paged out or unavailable on the page table due to '[demand paging](https://en.wikipedia.org/wiki/Demand\_paging)' and since HyperDbg could not perform paging in the VMX-root mode, it throws this error.

Thus, two situations might happen here.

**First, the address is invalid!**

If the address is not valid and not allocated by OS, surely you cannot put a hook on it or view its content.

**Second, the address is actually valid but still, we get this error.**

Some addresses might not be available on the RAM (paged out by the operating system) or the operating system did not allocate a physical memory for the address as it brings the page into the RAM only when an attempt is made to access it.

Another scenario is that the page is already in the RAM but from the current view of the process (based on the current CR3 register), it is not **presen**t in the page-table. For example, the address might be located in the '**kernel32**' or '**ntdll**' memory but since the current process never accessed some of the functions (pages) it is not present based on their paging base of the memory.

In the second scenario, you have two options forcing the operating system to bring the page into the memory or make it present in both [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode) and the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

### Access invalid address from VMI Mode

In this mode, virtual memory reading commands like [db, dc, dd, and dq](https://docs.hyperdbg.org/commands/debugging-commands/d) or assembler commands like [u, and u2](https://docs.hyperdbg.org/commands/debugging-commands/u), all access the memory from VMX non-root mode.  This means that accessing the memory will trigger a page-fault and if the address is actually valid, the OS either brings it to the RAM or makes it present to the process.

For example, assume that we want to apply the following EPT hook (e.g., by using the '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)').

```
HyperDbg> !epthook kernel32!LoadLibraryW script {
> printf("LoadLibrary called!\n"); 
> }
err, invalid address (c0000005)
address may be paged-out or unavailable on the page table due to 'demand paging'
please refer to https://docs.hyperdbg.org/tips-and-tricks/considerations/accessing-invalid-address for further information
```

If you access this function directly using the memory access commands or assembler commands,&#x20;

```
HyperDbg> u kernel32!LoadLibraryW
```

or

```
yperDbg> u kernel32!LoadLibraryW pid 1c0
```

Then when you run your EPT hook again, the address is valid this time!

```
HyperDbg> !epthook kernel32!LoadLibraryW script {
> printf("LoadLibrary called!\n"); 
> }
LoadLibrary called!
LoadLibrary called!
LoadLibrary called!
LoadLibrary called!
...
```

## Access invalid address from Debugger Mode

If you are in the debugger mode, the debuggee cannot switch between processes. At this point, you can use the '[.pagein](https://docs.hyperdbg.org/commands/meta-commands/.pagein)' command to inject a page-fault (#PF) and force the operating system to bring the page into the memory or make it present in the paging tables of the current process.

```
0: kHyperDbg> !epthook kernel32!LoadLibraryW script {
> printf("LoadLibrary called!\n"); 
> }
err, invalid address (c0000005)
address may be paged-out or unavailable on the page table due to 'demand paging'
please refer to https://docs.hyperdbg.org/tips-and-tricks/considerations/accessing-invalid-address for further information
```

In order to bring it to the memory, run the following command:

```
0: kHyperDbg> .pagein kernel32!LoadLibraryW
the page-fault is delivered to the target thread
press 'g' to continue debuggee (the current thread will execute ONLY one instruction and will be halted again)...

0: kHyperDbg> g
```

Once you run the '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' command the OS finds a chance to bring the page into the memory or make it present in the memory and it immediately pauses the debuggee again, then you can apply your EPT hook.

```
0: kHyperDbg> !epthook kernel32!LoadLibraryW script {
> printf("LoadLibrary called!\n"); 
> }

0: kHyperDbg> g
LoadLibrary called!
LoadLibrary called!
LoadLibrary called!
LoadLibrary called!
...
```

Please remember that these techniques only apply when the **address is actually valid**, if the address is not allocated and invalid, then it does not make sense to access them!

---
description: Methods to switch to the target process's memory layout (CR3)
---

# Switch to New Process Layout

### Please do not use this method until further notice.

{% hint style="danger" %}
HyperDbg has a design caveat of not relying on `GUEST_CR3` after the meltdown patch so we'll ignore modifying `cr3` on the debugger vmx-root side.

A patch to solve this problem is available but as it's an essential functionality in HyperDbg, we leave it for future versions with more tests.
{% endhint %}

When you want to switch to a new process using the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command, sometimes Windows will not load the target process in the running stage of Windows. Thus, it won't break again, and you won't end up in the target process.

This is since Windows might halt or suspend the process or threads of the target process are in a waiting state. So, Windows won't assign a CPU time slice to these processes.

In these cases, we can attach to the memory layout of these processes without switching to them.

For this purpose, we use the `.process list` command to find the **Directory Base** or **Kernel Cr3** of these processes.

```
2: kHyperDbg> .process list
PROCESS ffff948cba05f040
        Process Id: 0004        DirBase (Kernel Cr3): 00000000001aa002  Image: System

PROCESS ffff948cba0eb080
        Process Id: 006c        DirBase (Kernel Cr3): 0000000000263002  Image: Registry

...

PROCESS ffff948cbd8ba2c0
        Process Id: 03e8        DirBase (Kernel Cr3): 000000020e98e002  Image: svchost.exe
...
```

We find our target process. As you can see in the above example, our target process is **svchost.exe**, and the process Id is **3e8**.

Now, we can switch to the target process using the '[r](https://docs.hyperdbg.org/commands/debugging-commands/r)' command. We'll change the current **CR3** register to the target processes' **DirBase (Kernel Cr3)**, which is `20e98e002` in this case.

```
2: kHyperDbg> r cr3=20e98e002
```

Done! Now we're in the memory layout of the target process, and whatever script or command you use is applied to the target process memory.

Note that we didn't switch to the target process, and if you query for `$pid` you'll see the `$pid` of the wrong process, but in reality, we're in the target process memory.


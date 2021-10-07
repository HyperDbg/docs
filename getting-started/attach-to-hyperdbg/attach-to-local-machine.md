---
description: Local debugging in VMI Mode
---

# Attach to local machine

If you want to debug your local machine without any remote physical machine or nested virtualization \(like VMware Workstation, VirtualBox, Hyper-V, etc.\), you should use **local debugging**.

Generally, you can use most of the features of **HyperDbg.** Still, you should be cautious because if you unintentionally change any operating system's kernel structure that you did not suppose to, then a BSOD will happen.

Some features like a break to the debugger and step kernel-mode instructions are not possible in local debugging; however, you can use these features for user-mode apps without any limitation. Of course, you can use almost all the kernel-mode features.

For **local debugging**, you can run `.connect local` the command to connect to the local debugger.

```text
HyperDbg> .connect local
local debugging (vmi-mode)
```

You can then use the '[load](https://docs.hyperdbg.org/commands/debugging-commands/load)' command to load your module \(for instance, **vmm** module\).

```text
HyperDbg> load vmm
loading the vmm driver
current processor vendor is : GenuineIntel
virtualization technology is vt-x
vmx operation is supported by your processor
vmm module is running...
```

#### **When you connect to the local debugger, you are operating in** [**VMI Mode**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode)**.**


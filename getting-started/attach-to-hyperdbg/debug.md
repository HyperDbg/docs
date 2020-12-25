---
description: Remote debugging in VMI Mode and Debugger Mode
---

# Attach to a remote machine

If you have access to a remote physical machine or a nested virtualization environment like \(VMware Workstation, VirtualBox, Hyper-V, etc.\) then you can **operate** in both ****[**VMI Mode**](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#vmi-mode) and [**Debugger Mode**](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

In **VMI Mode**, you can't break the kernel mode and step through the kernel instructions; still, you can step and break user-mode applications. This mode needs a network connection \(TCP\).

In **Debugger Mode**, you can break the kernel mode and step through the kernel instructions. It needs a serial \(cable or virtual device\) to connect to the target machine.

If you don't want to break and step kernel-mode instructions, then use VMI Mode instead. It is because the serial cable is slow, so transferring high-rates of data is substantially slower.

### Connect to Debuggee \(VMI Mode\)

First, make sure you have access to the remote machine by pinging its IP address and check firewall rules. After that, run the following command in **debuggee \(guest\)**.

```text
HyperDbg >listen
listening on 50000 ...
```

The default port for **HyperDbg** is `50000`, but if you want to choose another port, then add an argument as the port to the listen \(e.g. `45000`\).

```text
HyperDbg >listen 45000
listening on 45000 ...
```

Now, go to your **debugger \(host\)** system and run the following command. Make sure to change the IP address and port.

```text
HyperDbg >.connect 192.168.1.10 50000
connected to 192.168.1.10:50000
```

After that, you see a connected message with an IP address of debugger \(host\) in debuggee \(guest\).

```text
HyperDbg >listen
listening on 50000 ...
connected to : 127.0.0.1:52830
```

You can see the state of the debugger by using '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' command.

### Connect to Debuggee \(Debugger Mode\)

For connecting in debugger mode, first, you need to provide a serial device.

#### **Physical Machine**

#### **VMware Workstation**

#### **VMware Player**

#### **VirtualBox**

#### **Hyper-V**

#### **KVM**

#### Parallels Desktop





\*\*\*\*


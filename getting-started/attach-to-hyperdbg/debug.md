---
description: Remote debugging in VMI Mode and Debugger Mode
---

# Attach to a remote machine

If you have access to a remote physical machine or a nested virtualization environment like \(VMware Workstation, VirtualBox, Hyper-V, etc.\), you can **operate** in both ****[**VMI Mode**](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#vmi-mode) and [**Debugger Mode**](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

In **VMI Mode**, you can't break the kernel mode and step through the kernel instructions; still, you can step and break user-mode applications. This mode needs a network connection \(TCP\).

In **Debugger Mode**, you can break the kernel mode and step through the kernel instructions. It needs a serial \(cable or virtual device\) to connect to the target machine.

If you don't want to break and step kernel-mode instructions, then use VMI Mode instead. It is because the serial cable is slow, so transferring high-rates of data is substantially slower.

## Connect to Debuggee \(Debugger Mode\)

For connecting in debugger mode, first, you need to provide a serial device.

### **Physical Machine**

In order to connect to a physical machine in debugger mode, you need a physical serial port. After that, connect your target machine \(debuggee\) to another machine.

Debuggee needs to support Intel VMX and Intel EPT; however, the debugger does not need to support any special CPU feature and can be run on any machine, including a machine with an AMD processor.

If you want to have a kernel debug connection, first, you should run the following command in a debugger \(host\). As you can see, you can change the `com3`to your COM port that is connected to the debuggee. You can see connected COM ports on the device manager.

```text
HyperDbg> .debug remote serial 115200 com3
```

If you want to use a named pipe instead of a COM port, you can execute the following command in the debugger \(Host\).

```text
HyperDbg> .debug remote namedpipe \\.\pipe\HyperDbgPipe
```

After you tell the debugger to listen on a COM port or a named pipe, now you can run the following command in the debuggee.

```text
HyperDbg> .debug prepare serial 115200 com2
```

### **VMware Workstation**

In order to run HyperDbg on a VMware Workstation machine, first, turn off your guest machine then, you need to enable **Nested Virtualization**. Open your virtual machine and click on Edit **virtual machine settings**.

VMware Player doesn't have nested virtualization, so it cannot be used for HyperDbg debugging.

![Editing VM Settings](../../.gitbook/assets/vmware-debug1.png)

After that, click on **Virtualize Intel VT-x/EPT or AMD-V/RVI** and **Virtualize IOMMU \(IO memory management unit\)**.

![Enabling Nested-Virtualization](../../.gitbook/assets/vmware-debug2.png)

Next, click on **Add...** then choose **Serial Port** and click on **Finish**.

![Adding a serial device](../../.gitbook/assets/vmware-debug3.png)

Now, click on **Use named pipe:** and add a name for your named pipe. 

Your name should start with **`\\.\pipe\`** . For example, choose **`\\.\pipe\HyperDbgDebug`**.

Make sure to enable **Yield CPU on poll**.

![Change serial device configuration](../../.gitbook/assets/vmware-debug4.png)

Now it's time to create a kernel debug connection. First of all, run the following command on the host \(debugger\). You should change the named pipe address to whatever name you chose on the previous part.   

```text
HyperDbg> .debug remote namedpipe \\\\.\\pipe\\HyperDbgPipe
```

After you tell the debugger to listen on a COM port or a named pipe, now you can run the following command in the debuggee \(guest\).

```text
HyperDbg> .debug prepare serial 115200 com2
```

Most of the times, if the serial port is the only serial device that you add to the virtual machine, then the name of the connected port is `com2`. However, you can see the exact name of the COM port on the guest's device manager.

{% hint style="info" %}
If you see an error for driver signature enforcement, please visit [here](https://docs.hyperdbg.com/using-hyperdbg/examples/connecting-to-hyperdbg#driver-signature-enforcement-error).
{% endhint %}

## Connect to Debuggee \(VMI Mode\)

First, make sure you have access to the remote machine by pinging its IP address and check firewall rules. After that, run the following command in **debuggee \(guest\)**.

```text
HyperDbg> listen
listening on 50000 ...
```

The default port for **HyperDbg** is `50000`, but if you want to choose another port, then add an argument as the port to the listen \(e.g. `45000`\).

```text
HyperDbg> listen 45000
listening on 45000 ...
```

Now, go to your **debugger \(host\)** system and run the following command. Make sure to change the IP address and port.

```text
HyperDbg> .connect 192.168.1.10 50000
connected to 192.168.1.10:50000
```

After that, you see a connected message with an IP address of debugger \(host\) in debuggee \(guest\).

```text
HyperDbg> listen
listening on 50000 ...
connected to : 127.0.0.1:52830
```

You can see the state of the debugger by using the '[.status](https://docs.hyperdbg.com/commands/meta-commands/.status)' command.


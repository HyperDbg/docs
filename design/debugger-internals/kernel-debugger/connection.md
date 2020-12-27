---
description: The connection mechanism of HyperDbg in Debugger mode
---

# Connection

Currently, the only way to communicate to the kernel debugger is over the serial port. For future versions, we will add other IRQL-compatible mechanisms to communicate.

For this purpose, we used **kdserial** from Windows SDK to connect to the debuggee. The connection over the serial port is initialized from the user-mode controller of HyperDbg.

First, we let Windows to initialize the serial port from standard entry points like `CreateFile`. After that, we set the baud-rate and timeout details. We let Windows to initialize the handle because this way we make sure that Windows won't give the handle of this device to any other application and no other application is able to send anything over this port.

The mechanism to pause the debuggee is also implemented in user-mode. It is because we are not trying to handle each pause requests from the debugger in polling mode as this is not an optimized way.

Instead of this, we use the interrupt-mode of the serial device in the user-mode. This way, whenever the debugger needs to pause the debuggee, first it sends a request and as it's in interrupt-mode then Windows notifies the user-mode application about the new request from the debugger. 

If the request is valid then the user-mode application sends an IOCTL to the kernel and from that IOCTL we execute a VMCALL to enter all cores to the vmx-root mode.

In vmx-root mode, we halt all cores, and as the IF flag of RFLAGS is cleared, so no interrupt is allowed and this way we halt all the cores and check for a new command from the debugger in polling mode.


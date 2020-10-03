---
description: Kernel Message Tracing Overflow
---

# Message Overflow

Kernel buffers for transferring data safely to the user-mode \(debugger\) can be filled and if the debugger doesn't find time to transfer all messages, then the new messages will replace the previous messages and as a result, you lose your messages which are not yet transferred to the user-mode. 

In the rest of this document, you will learn the important factors that can decrease such scenarios. Take a look at [Customize Build](https://docs.hyperdbg.com/tips-and-tricks/misc/customize-build) for more information.



### Use Conditions

### Change Reading Delay 

hprdbgctrl.cpp ReadIrpBasedBuffer\(\) 

```c
...
        //
        // Clear the buffer
        //
        ZeroMemory(OutputBuffer, UsermodeBufferSize);

        Sleep(200); // we're not trying to eat all of the CPU ;)

        Status = DeviceIoControl(
            Handle,               // Handle to device
            IOCTL_REGISTER_EVENT, // IO Control code
            &RegisterEvent,       // Input Buffer to driver.
            SIZEOF_REGISTER_EVENT *
                2, // Length of input buffer in bytes. (x 2 is bcuz as the
                   // driver is x64 and has 64 bit values)
            OutputBuffer,       // Output Buffer from driver.
            UsermodeBufferSize, // Length of output buffer in bytes.
            &ReturnedLength,    // Bytes placed in buffer.
            NULL                // synchronous call
        );
...
```

### Not Use Immediate Messaging

```c
/**
 * @brief Use immediate messaging (means that it sends each message when they
 * recieved and do not accumulate them) it works only if you set
 * UseDbgPrintInsteadOfUsermodeMessageTracking to FALSE
 */
#define UseImmediateMessaging FALSE
```

### Increas Packet Storage Capacity

```c
/* Default buffer size */
#define MaximumPacketsCapacity 1000 // number of packets
```

### Increase Each Chunk's Size

```c
#define PacketChunkSize                                                        \
  1000 // NOTE : REMEMBER TO CHANGE IT IN USER-MODE APP TOO
```

\*\*\*\*


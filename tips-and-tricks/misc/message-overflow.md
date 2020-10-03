---
description: Kernel Message Tracing Overflow
---

# Message Overflow

Kernel buffers for transferring data safely to the user-mode \(debugger\) can be filled and if the debugger doesn't find time to transfer all messages, then the new messages will replace the previous messages and as a result, you lose your messages which are not yet transferred to the user-mode. 

In the rest of this document, you will learn the important factors that can decrease such scenarios. Take a look at [Customize Build](https://docs.hyperdbg.com/tips-and-tricks/misc/customize-build) for more information.

### Use Conditions

[Condition code](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/how-to-create-a-condition) buffers are one of the most important parts of **HyperDbg**. You can use this feature to avoid triggering unnecessary events \(actions\) by creating conditions that filter the results for you in assembly form in both kernel-mode and vmx-root mode.

### Change Reading Delay 

You can change `hprdbgctrl.cpp` file's **ReadIrpBasedBuffer\(\)** method to decrease the delay between reading each message from the kernel. You can change `Sleep(200);` to a lower time \(ms\), so, you can read messages faster.

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

By default, **HyperDbg** accumulates messages in a separate buffer and it won't send them immediately to the user-mode, this accumulation causes more messages to be delivered to the user-mode by one IRP packet so kernel messages will be transferred to the user-mode by a higher speed.

If you need to see messages immediately after each one message then set this option to `TRUE`. However, it kills the performance as sending buffers to the user-mode involves various and heavy functions.

If you set this option to `FALSE` \(**default**\), then **HyperDbg** accumulates \(**~5** or more based on message length and chunk size\) messages and when the buffer is full then it sends the buffer to the user-mode **CLI** or **GUI**.

```c
/**
 * @brief Use immediate messaging (means that it sends each message when they
 * recieved and do not accumulate them) it works only if you set
 * UseDbgPrintInsteadOfUsermodeMessageTracking to FALSE
 */
#define UseImmediateMessaging FALSE
```

### Increase Packet Storage Capacity

This is the most important factor in message tracing, if you increase the following value then more messages will be stored in **HyperDbg** buffers; thus, they won't be replaced until the buffer is full, for example, if set this value to **1000** then if you produce **1000** messages that are not delivered to the user-mode then **HyperDbg** replaces new messages with older messages, and in the case, if you set this value to **2000** then you have more capacity; thus, the buffers will have the capacity to hold twice more messages. 

However, keep in mind that by increasing the following value, **HyperDbg** occupies more non-paged pools \(RAM\) so you need to balance your message capacity with your memory \(RAM\). 

```c
/* Default buffer size */
#define MaximumPacketsCapacity 1000 // number of packets
```

### Increase Each Chunk's Size

The following option, shows the capacity of each packet in **HyperDbg**'s message tracing. If you have long messages or buffers, then you can increase this value. 

If you increased it then **HyperDbg** accumulates more messages and won't send them immediately to the user-mode \(as described in **Not Use Immediate Messaging** above\), this will lead to better performance; however, messages will be delivered by a delay \(for accumulation\). 

```c
#define PacketChunkSize                                                        \
  1000 // NOTE : REMEMBER TO CHANGE IT IN USER-MODE APP TOO
```

\*\*\*\*


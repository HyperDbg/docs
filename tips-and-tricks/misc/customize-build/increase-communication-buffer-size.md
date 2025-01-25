---
description: Increasing the buffer size for kHyperDbg communication
---

# Increase Communication Buffer Size

If the [kHyperDbg](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/signatures#kernel-debugging-debugger-mode) communication displays an error message stating that your script or buffer is too large to be saved in the default communication buffer, this means that the buffer size has exceeded the maximum size that can be sent to the debuggee (e.g., "`err, buffer is above the maximum buffer size that can be sent to debuggee (93080 > 40960)`").

HyperDbg initially allocates several pages for sending and receiving communication buffers. If the buffer size surpasses the maximum size, then the above-mentioned error message will be displayed.

To fix this problem, you must build a customized version of HyperDbg that uses the most suitable size for your requirements.

Navigate to the source code and locate the **SDK** and Headers folders. In the header files, search for `MaxSerialPacketSize` and you will see the following code:

```clike
/**
 * @brief size of buffer for serial
 * @details the maximum packet size for sending over serial
 *
 */
#define MaxSerialPacketSize 10 * NORMAL_PAGE_SIZE
```

The above macro defines the default buffer size. You must increase it according to your buffer size estimation provided in the error message.

After that, [rebuild](https://docs.hyperdbg.org/getting-started/build-and-install) HyperDbg, and use your custom-made version of the debugger.

---
description: Description of 'flush' command in HyperDbg.
---

# flush \(flush and remove kernel pending buffers and messages\)

### Command

> flush

### Syntax

> flush

### Description

This command removes all the possible pending buffers and messages from **all the commands \(not just the command that you disabled or removed\)** that are stored to be received by the user-mode from the kernel-mode are removed and when you press '[g](https://docs.hyperdbg.com/commands/debugging-commands/g)', you no longer see any results from previous commands; however, some commands might continue generating new messages, and those new messages won't be removed. 

### Parameters

None

### IOCTL

None

### **Remarks**

None

### Requirements

None

### Related




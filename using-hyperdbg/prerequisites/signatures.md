---
description: Different signatures in HyperDbg
---

# Signatures

There are a few signatures in HyperDbg that demonstrate basic debugging details from the debuggee. In this document, we'll talk about these signatures and their meanings.

### Local Debugging (VMI Mode)

If you're not connected to any instance of **HyperDbg**, or if you're debugging the **local** computer in [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode), then you'll see the following signature.

```
HyperDbg> 
```

### Kernel Debugging (Debugger Mode)

When you're connected to a remote debuggee in [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode) by using the '[.debug](https://docs.hyperdbg.org/commands/meta-commands/.debug)' command, you'll see the `kHyperDbg` signature. The first number is the current operating **core** number. For example, the following signature shows that we're running our commands in core `0x2`. Note that the core number is in **hex** format.

```
2: kHyperDbg> 
```

### User Debugging (VMI Mode)

User-mode debugging has two different signatures, the first signature is for 32-bit module debugging, and the second signature is for 64-bit module debugging.

#### Debugging a 32-bit Module

The signature for 32-bit debugging is `u86HyperDbg` while the first number shows the active **Process Id** and the second number is the **Thread Id**. For example, the following signature is a 32-bit debugging for a process with Process Id equal to `0x228c` and Thread Id equal to `0x13fc`. The Process Id and the Thread Id are in **hex** format.

```
228c:13fc u86HyperDbg>
```

#### Debugging a 64-bit Module

The signature for 64-bit debugging is `u64HyperDbg` . The numbers are exactly like 32-bit debugging. The first number shows the active **Process Id** and the second number is the **Thread Id**. For example, the following signature is a 64-bit debugging for Process Id equal to `0x2300` and Thread Id equal to `0x1620`. The Process Id and the Thread ID are in **hex** format.

```
2300:1620 u64HyperDbg>
```

### Remote Debugging (VMI Mode)

If you're connected to a remote machine using '[.listen](https://docs.hyperdbg.org/commands/meta-commands/.listen)', and '[.connect](https://docs.hyperdbg.org/commands/meta-commands/.connect)' commands, the signature starts with the **IP** of the debuggee and the **port** of the connection to the debuggee. For example, in the following signature, we're connected to a debuggee with an IP address equal to `192.168.1.10`, and the port address for the connection `50000`. The IP address and the port number are in **decimal** format.

```
[192.168.1.10:50000] HyperDbg> 
```


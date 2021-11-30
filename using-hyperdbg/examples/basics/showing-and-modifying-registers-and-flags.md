---
description: using the 'r' command
---

# Showing & Modifying Registers and Flags

You can view and modify registers only in Debugger Mode. It's possible using the 'r' command. HyperDbg supports wide list of registers and also support to view and modify each flag separately.

A list of supported registers are available here.

If you want to read all the registers, use the following command.

```clike
0: kHyperDbg> r
RAX=ffff948cc1902d40 RBX=ffff948cbfefb520 RCX=0000000000000022
RDX=0000000000000000 RSI=0000000000000000 RDI=ffff948cc00c30e0
RIP=fffff80168d91312 RSP=ffff930548474688 RBP=0000000000000002
R8=0000000000000000  R9=0000000000000000  R10=0000000048564653
R11=0000564d43414c4c R12=4e4f485950455256 R13=0000000000000000
R14=ffff948cc00c30e0 R15=ffff948cc19bfe10 IOPL=00
OF 0  DF 0  IF 1  SF  0
ZF 1  PF 1  CF 0  AXF 0
CS 0010 SS 0018 DS 002b ES 002b FS 0053 GS 002b
RFLAGS=0000000000040246
```

If you want to read a special register, use its name after the 'r' command.

```clike
0: kHyperDbg> r @rax
rax=ffff948cc1902d40

0: kHyperDbg> r @rip
rip=fffff80168d91312
```

Also, you can modify the value of a register by using '=' and set it to a hex value.

```clike
0: kHyperDbg> r @rax = 55

0: kHyperDbg> r @rax
rax=0000000000000055
```

It's also possible to modify the each flag of the RFLAGS.

```
0: kHyperDbg> r @cf
cf=0000000000000000

0: kHyperDbg> r @cf = 1

0: kHyperDbg> r @cf
cf=0000000000000001
```

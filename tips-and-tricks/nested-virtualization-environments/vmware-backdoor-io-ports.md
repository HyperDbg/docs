---
description: >-
  Description about the state of using !ioin and !ioout in VMware nested
  virtualization
---

# VMware backdoor I/O ports

If you want to use **!ioin** and **!ioout** commands in a VMware nested-virtualization environment, you should know that VMware uses I/O port **0x5658/0x5659** as a [backdoor ](https://sites.google.com/site/chitchatvmback/backdoor)for its internal connection between VMware tools and VMM.

If you want to monitor all I/O ports for IN and OUT, then as we grab all IN/OUT vm-exits, then the VMware tools will crash, but if you need a special port or any port other than **0x5658/0x5659**, then there is no problem.

When VMware tools crashed, you can continue with VM and HyperDbg without problem, but special features relating to VMware tools will no longer work.

For example, the screen will not be scaled to minimize and maximize and guest to host and host to guest copy/paste features will not work.

We didn't observe such behavior in other nested-virtualization environments other than VMware.


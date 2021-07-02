---
description: How to run HyperDbg on VMware nested-virtualization?
---

# Run HyperDbg on VMware

In order to run HyperDbg on VMware, you should enable nested-virtualization on it.

First of all, make sure to update your VMWare to the latest version \(it’s important as VMWare sometimes has problems with nested-virtualization on out-dated versions\).

Click on "Edit virtual machine settings" on your target VM.

![](../../.gitbook/assets/nested-virtualization-vmware1.png)

Now, enable the following options on your machine.

![](../../.gitbook/assets/nested-virtualization-vmware2.png)

Note that you have a limitation on using **!ioin** and **!ioout** in VMware nested-virtualization. \([VMware backdoor I/O port](https://docs.hyperdbg.com/tips-and-tricks/nested-virtualization-environments/vmware-backdoor-io-ports)\).

{% hint style="warning" %}
**HyperDbg** will not hide traces from VMware or any other nested-virtualization environments. Malware or any application can detect the presence of VMWare, and regular anti-vm techniques detect the VMware environment.
{% endhint %}




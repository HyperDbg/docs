---
description: Compatibility between VMware and Hyper-V
---

# Supporting VMware/Hyper-V

At the time of writing this documentation, Hyper-V and VMware Workstation are compatible with each other, which means that you can run Hyper-V and VMware together. Still, we prefer not to do it, as it's slower, so if you want to disable the Hyper-V completely or you see the following message in VMWare:

_VMware Workstation's nested-virtualization and Hyper-V are not compatible. Disable the Hyper-V role from the system before running VMware Workstation._

Then, you can run the following command (as administrator) and restart your computer.

```
bcdedit /set hypervisorlaunchtype off
```

On the other hand, if you want to re-enable Hyper-V, you can run the following command (as administrator) and restart your computer.

```
bcdedit /set hypervisorlaunchtype auto
```

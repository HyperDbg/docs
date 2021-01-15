---
description: How to run HyperDbg on Hyper-V nested-virtualization?
---

# Run HyperDbg on Hyper-V

{% hint style="danger" %}
Hyper-V has an unknown problem with HyperDbg and HyperDbg-style hypervisors. You might encounter unknown errors. We left it for future support.
{% endhint %}

In order to run HyperDbg on Hyper-V, you should enable nested-virtualization on it.

For Hyper-V, we can enable nested-virtualization for the target virtual machine by running the following command on **Powershell** :

```
Set-VMProcessor -VMName PutYourVmNameHere -ExposeVirtualizationExtensions $true
```

{% hint style="info" %}
Note that instead of **PutYourVmNameHere**, put the name of your virtual machine that you want to enable nested virtualization for it.
{% endhint %}

And if you need to disable it, you can run:

```text
Set-VMProcessor -VMName PutYourVmNameHere -ExposeVirtualizationExtensions $false
```


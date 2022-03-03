# User-mode Debugging

{% hint style="danger" %}
The user-mode debugger is still in the beta version and not stable. We decided to exclude it from this release and release it in future versions. If you want to test the user-mode debugger in **VMI Mode**, you should build HyperDbg with special instructions. Please follow the instruction [here](https://docs.hyperdbg.org/getting-started/build-and-install#activating-user-mode-debugger).

In contrast with the kernel debugger, the user debugger is still very basic and needs a lot of tests and improvements. We **highly recommend** not to run the user debugger in your bare metal system. Instead, run it on a [supported virtual machine](https://docs.hyperdbg.org/tips-and-tricks/nested-virtualization-environments/supported-virtual-machines) to won't end up with a Blue Screen of Death (BSOD) in your primary device. Please keep reporting the issues to improve the user debugger.
{% endhint %}

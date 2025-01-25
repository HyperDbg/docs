---
description: Description about HyperDbg Transparent-mode
---

# Transparent Mode

[**Transparent mode**](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#transparent-mode) is an **anti-anti-debugging** and an **anti-anti-hypervisor** solution for HyperDbg.

If you enable this mode, then HyperDbg tries to make itself transparent from anti-debugging and anti-hypervisor methods; however, it won't guarantee 100% transparency, but it makes it substantially harder for the anti-debugging methods.

To enable this mode, you should use the '[!hide](https://docs.hyperdbg.org/commands/extension-commands/hide)' command.

You can use **Transparent Mode** in both **VMI Mode** and **Debugger Mode**.

Transparent mode is under active development, and on each version, we might add new methods to this mode to make sure that HyperDbg is transparent. However, this mode still needs a lot of contributions.

If you think you can add new methods for transparency, don't forget to contribute or ping us on [GitHub](https://github.com/HyperDbg/HyperDbg).[\
](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)

---
description: Description about HyperDbg Transparent-mode
---

# Transparent Mode

**Transparent-mode** is an **anti-anti-debugging** and an **anti-anti-hypervisor** solution for HyperDbg.

If you enable this mode, then HyperDbg tries to make itself transparent from anti-debugging and anti-hypervisor methods and also not to reveal the presence of hypervisor on timing and microarchitectural attacks; however, it won't guarantee 100% transparency, but it makes it substantially harder for the anti-debugging methods.

To enable this mode, you should use '[!measure](https://docs.hyperdbg.org/commands/extension-commands/measure)' and '[!hide](https://docs.hyperdbg.org/commands/extension-commands/hide)' commands.

You can use **Transparent Mode** in both **VMI Mode** and **Debugger Mode**.

For enabling this mode, first, you should use the '[!measure](https://docs.hyperdbg.org/commands/extension-commands/measure)' command. This command uses statistical methods to measure and provide the details for the transparent-mode of HyperDbg for defeating anti-debugging and anti-hypervisor methods.

This command should be run before you '[load](https://docs.hyperdbg.org/commands/debugging-commands/load)' the debugger or before connecting to the debugger, and after that, you can use '[!hide](https://docs.hyperdbg.org/commands/extension-commands/hide)' command.

```
HyperDbg> !measure
```

If you want to use the hardcoded results and statistics for a not-running hypervisor machine, you can use the following command to apply the default measurements.

```
HyperDbg> !measure default
```

After that, you should use the '[!hide](https://docs.hyperdbg.org/commands/extension-commands/hide)' command, for example, if you want to apply the transparent features to process id `2a78` you can use the following command.

```
HyperDbg> !hide pid 2a78
```

If you want to apply to a process name, then use the following command.

```
HyperDbg> !hide name procexp.exe
```

Transparent-mode is under active development, and on each version, we add new methods to this mode to make sure that HyperDbg is transparent. However, this mode still needs a lot of contributions.

If you think you can add new methods for transparency, don't forget to contribute or ping us on [GitHub](https://github.com/HyperDbg/HyperDbg).[\
](https://docs.hyperdbg.org/tips-and-tricks/considerations/the-unsafe-behavior)

---
description: The command style of HyperDbg
---

# Command Style

For ease of use, Windbg uses the same command-style of Windbg, which means there are different kinds of commands in HyperDbg.

‌ In HyperDbg we have 3 types of commands , "regular", "meta", extension".

‌ Regular commands, e.g. "test" apply to the debugging session. These are the ones controlling and getting information from the debugging target.

‌ Meta commands are prefixed with a dot, e.g., ".test". Meta commands apply to the debugger itself means that these commands are the ones controlling debugger itself, not the debugging target.

‌ Extension commands are prefixed with an exclamation mark, e.g. !test, which is defined in debugger extensions and features.

‌ Here is a full example of a command with a detailed description of each field, used in documentation.

{% embed url="https://github.com/SinaKarvandi/HyperDbg/blob/master/docs/CommandsDocStyle.pdf" %}

{% hint style="success" %}
HyperDbg commands are case-insensitive.
{% endhint %}


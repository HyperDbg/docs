---
description: The command style of HyperDbg
---

# Command Style

For ease of use, HyperDbg uses the same command-style as Windbg, which means there are different kinds of commands in HyperDbg.

‌In HyperDbg we have 3 types of commands , "**regular**", "**meta**", "**extension**".

‌**Regular commands**, e.g., "test", apply to the debugging session. These are the ones controlling and getting information from the debugging target.

‌**Meta commands** are prefixed with a dot, e.g., ".test". Meta commands apply to the debugger itself, meaning that these commands are the ones controlling the debugger itself, not the debugging target.

‌**Extension commands** are prefixed with an exclamation mark, e.g. "!test", which is defined in debugger extensions and features.

‌Here is a full example of a command with a detailed description of each field used in documentation.

{% file src="../../.gitbook/assets/commandsdocstyle.pdf" %}
Download Command Documentation Style
{% endfile %}

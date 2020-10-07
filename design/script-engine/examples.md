---
description: Example of valid expressions in evaluator
---

# Examples

### Examples

```text
test=str(@rcx)
test=dq(@rcx)
test=$proc+@rdx
test=poi(@rax+a0)
test1=wstr(poi($proc+10));test2=str(poi($proc+10));
test=dw(NtCreateFile+10)
test=dw(NtCreateFile+@rcx+($proc|3+poi(poi(@rax))))
```

### Description

`test=str(@rcx)`

One field, called '**test**', which will be read as an ASCII string pointed by **rcx** register.

`test=dq(@rcx)`

One field, called '**test**', which will be read as an 8-byte hex pointed by **rcx** register.

`test=$proc+@rdx`

One field, called '**test**', which is equivalent to **$proc** \(current `_EPROCESS`\) added to the **rdx** register.

`test=poi(@rax+a0)` 

One field, called '**test**', which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

`test1=wstr(poi($proc+10));test2=str(poi($proc+10));`

Three fields, first '**test1**' which is **$proc** \(current `_EPROCESS`\) added with **0x10** then a dereference occurs and the target pointer in the dereferenced location is shown as a wide-char string.

Second, '**test2**' which is **$proc** \(current `_EPROCESS`\) added with **0x10** then a dereference occurs and the target pointer in the dereferenced location is shown as an ASCII string.

`test=dw(NtCreateFile+10)`

One field, called '**test**', **NtCreateFile** location is added with **0x10** then a DWORD value from the target address \(**NtCreateFile+10**\) is shown.

{% hint style="success" %}
By default, **HyperDbg** interprets the numbers as **hex** \(base **16**\), if you want to specify other forms of a number you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix \(16, 10, or 8\). You can override the default radix by specifying the **0x** prefix \(**hexadecimal**\), the **0n** prefix \(**decimal**\), the **0t** prefix \(**octal**\), or the **0y** prefix \(**binary**\).
{% endhint %}


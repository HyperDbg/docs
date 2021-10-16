---
description: An example of valid expressions to read the state of the system
---

# view system state (registers, memory, variables)

### Examples

```
? printf("Result : %s", @rcx));
? printf("Process name: %s", $pname);
? print(dq(@rcx));
? print($proc+@rdx);
? print(poi(@rax+a0));
? printf("Result : %ws", poi($proc+10)); printf("Result : %s", poi($proc+10));
? print(dw(NtCreateFile+10));
? print(dw(NtCreateFile+@rcx+($proc|3+poi(poi(@rax)))));
```

### Description

```clike
? printf("Result : %s", @rcx);
```

Print data as an ASCII string pointed by **rcx** register.

```clike
? printf("Process name: %s", $pname);
```

Print the process name.

```clike
? print(dq(@rcx));
```

Print data as an 8-byte hex, pointed by **rcx** register.

``

Print value pointed by `$proc+@rdx` which **$proc** is equivalent to current `_EPROCESS` added to the **rdx** register.

`? print(poi(@rax+a0));`

Print value of an address, which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

`? print("Result : %ws", poi($proc+10)); printf("Result : %s", poi($proc+10));`

Two values, first is **$proc** (current `_EPROCESS`) added with **0x10**, then a dereference occurs, and the target pointer in the dereferenced location is shown as a wide-char string.

Second is **$proc** (current `_EPROCESS`) added with **0x10**, then a dereference occurs, and the target pointer in the dereferenced location is shown as an ASCII string.

`? print(dw(NtCreateFile+10));`

One value, which is **NtCreateFile** location that added with **0x10**, then a DWORD value from the target address (**NtCreateFile+10**) is shown.

`? print(dw(NtCreateFile+@rcx+($proc|3+poi(poi(@rax)))));`

Print the DWORD data located at **NtCreateFile** added to **rcx** register, then all of them are added to the result of **rax** register dereferenced twice and added to the **$proc** which is bitwise **OR (|)** by **3**.

{% hint style="success" %}
By default, **HyperDbg** interprets the numbers as **hex** (base **16**). If you want to specify other forms of a number, you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix (16, 10, or 8). You can override the default radix by specifying the **0x** prefix (**hexadecimal**), the **0n** prefix (**decimal**), the **0t** prefix (**octal**), or the **0y** prefix (**binary**).
{% endhint %}

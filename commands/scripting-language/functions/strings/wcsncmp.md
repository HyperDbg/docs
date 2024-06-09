---
description: Description of the 'wcsncmp' function in HyperDbg Scripts
---

# wcsncmp

### Function

> wcsncmp

### Syntax

> wcsncmp( WStr1, WStr2, Num );

### Parameters

**\[WStr1]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a wide-character string to compare with **WStr2**.

**\[WStr2]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a wide-character string to compare with **WStr1**.

**\[Num]**

Maximum number of characters to compare.

### Description

Compares up to _**Num**_ characters of the wide-character string _**WStr1**_ to the wide-character string _**WStr2**_.

If the characters are equal, the comparison continues with the next pair of characters. This process repeats until the characters differ, a terminating null character is encountered, or _**Num**_ characters have been compared in both strings, whichever occurs first.

{% hint style="info" %}
Wide-character strings are started with an **L** character before the quotations. For example **L"This is a wide-character string"**.
{% endhint %}

### Return value

Returns an integer value indicating the relationship between the strings:\


| Return Value | Indicates                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------- |
| <0           | The first wide-character that does not match has a lower value in _WStr1_ than in _WStr2._   |
| 0            | The contents of both wide-character strings are equal.                                       |
| >0           | The first wide-character that does not match has a greater value in _WStr1_ than in _WStr2._ |

### Examples

``wcsncmp(fffff806`6de00000, @rax+c0, 0n12);``

Compare the the first 12 (decimal) characters of  the wide-character string located at ``fffff806`6de00000`` with the wide-character string located at `@rax+c0`.

`wcsncmp(@rcx, L"Test WString", 12);`

Compare the first 0x12 (hexadecimal) characters of the wide-character string located at `@rcx` with the wide-character string `L"Test WString"`.

`wcsncmp(L"Test WString1", L"Test WString2", 0n10);`

Compare the first 10 (decimal) characters of the wide-character string `L"Test WString1"` with the wide-character string `L"Test WString2"`.

### Remarks

This function checks for the validity of both the source and the destination address before the comparison.&#x20;

The support for this function is added from **v0.9**.

### Related

[strcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strcmp)

[strncmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strncmp)

[memcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcmp)

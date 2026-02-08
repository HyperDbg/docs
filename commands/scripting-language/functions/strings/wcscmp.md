---
description: Description of the 'wcscmp' function in HyperDbg Scripts
---

# wcscmp

### Function

> wcscmp

### Syntax

> wcscmp( WStr1, WStr2 );

### Parameters

**\[WStr1]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a wide-character string to compare with **WStr2**.

**\[WStr2]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) or a wide-character string to compare with **WStr1**.

### Description

Compares the wide-character string _**WStr1**_ to the wide-character string _**WStr2**_.

{% hint style="info" %}
Wide-character strings are started with an **L** character before the quotations. For example **L"This is a wide-character string"**.
{% endhint %}

### Return value

Returns an integer value indicating the relationship between the strings:<br>

| Return Value | Indicates                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------- |
| <0           | The first wide-character that does not match has a lower value in _WStr1_ than in _WStr2._   |
| 0            | The contents of both wide-character strings are equal.                                       |
| >0           | The first wide-character that does not match has a greater value in _WStr1_ than in _WStr2._ |

### Examples

``wcscmp(fffff806`6de00000, @rax+c0);``

Compare the wide-character string located at ``fffff806`6de00000`` with the wide-character string located at `@rax+c0`.

`wcscmp(@rcx, L"Test WString");`

Compare the wide-character string located at `@rcx` with the wide-character string `L"Test WString"`.

`wcscmp(L"Test WString1", L"Test WString2");`

Compare the wide-character string `L"Test WString1"` with the wide-character string `L"Test WString2"`.

### Remarks

This function checks for the validity of both the source and the destination address before the comparison.&#x20;

The support for this function is added from **v0.7**.

### Related

[strcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strcmp)

[strncmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strncmp)

[wcsncmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcsncmp)

[memcmp](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/memcmp)

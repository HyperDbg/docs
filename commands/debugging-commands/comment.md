---
description: 'Description of ''#'' command in HyperDbg.'
---

# \# \(comment in batch scripts\)

## Command

> \#

## Syntax

> \# ...

## Description

Comments a line of code or batch script.

{% hint style="success" %}
In HyperDbg's batch scripts '`#`' is used as the comment character. However, in the script engine, it uses a C-like [commenting style](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#comments).
{% endhint %}

## Parameters

**\[...\]**

```text
      Everything after this command is interpreted as a comment.
```

## Examples

The following example shows the comments style in HyperDbg.

```bash
# This is a comment.
.connect local # This is an inline comment.
```

## IOCTL

None

## **Remarks**

None

## Requirements

None

## Related

None


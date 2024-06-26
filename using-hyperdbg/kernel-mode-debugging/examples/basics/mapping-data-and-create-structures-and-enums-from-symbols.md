---
description: Using the 'dt' and the 'struct' commands
---

# Mapping Data & Create Structures, and Enums From Symbols

In this example, we'll see how to change the process name of the **notepad.exe** in the Task Manager by utilizing the '[dt](https://docs.hyperdbg.org/commands/debugging-commands/dt)' and the '[struct](https://docs.hyperdbg.org/commands/debugging-commands/struct)' commands of the HyperDbg.

From our previous Windows internals knowledge, we know that the process name is available in the `SeAuditProcessCreationInfo` field of the process's `_EPROCESS`.

Note that there is an `ImageFileName` field in the `_EPROCESS`. However, it's not what we're looking for as it's not the field shown in the Task Manager.

After looking at the `_EPROCESS`, we see that the `SeAuditProcessCreationInfo` is of the `_SE_AUDIT_PROCESS_CREATION_INFO` type. Let's see this structure recursively to see how Windows stores the process name.

```clike
HyperDbg> struct _SE_AUDIT_PROCESS_CREATION_INFO
typedef struct _SE_AUDIT_PROCESS_CREATION_INFO
{
  /* 0x0000 */ struct _OBJECT_NAME_INFORMATION* ImageFileName;
} SE_AUDIT_PROCESS_CREATION_INFO, *PSE_AUDIT_PROCESS_CREATION_INFO; /* size: 0x0008 */


HyperDbg> struct _OBJECT_NAME_INFORMATION
typedef struct _OBJECT_NAME_INFORMATION
{
  /* 0x0000 */ struct _UNICODE_STRING Name;
} OBJECT_NAME_INFORMATION, *POBJECT_NAME_INFORMATION; /* size: 0x0010 */


HyperDbg> struct _UNICODE_STRING
typedef struct _UNICODE_STRING
{
  /* 0x0000 */ uint16_t Length;
  /* 0x0002 */ uint16_t MaximumLength;
  /* 0x0004 */ long Padding_0;
  /* 0x0008 */ wchar_t* Buffer;
} UNICODE_STRING, *PUNICODE_STRING; /* size: 0x0010 */
```

As you can see, we've reached a `_UNICODE_STRING` structure, and this is where Windows stores the process name. Now let's open **notepad.exe**.

![](../../../../.gitbook/assets/notepad-process-in-task-manager.PNG)

As demonstrated, the Process ID is **5616** in decimal. We can convert it to the hex format by using the '[.formats](https://docs.hyperdbg.org/commands/meta-commands/.formats)' command by adding a **0n** prefix which indicates that the value is in decimal format.

```clike
1: kHyperDbg> .format 0n5616
evaluate expression:
Hex :        00000000`000015f0
Decimal :    5616
Octal :      12760
Binary :     00000000 00000000 00000000 00000000 00000000 00000000 00010101 11110000
Char :       ........
Time :       04/19/22 - 12:53AM
Float :      0.00 +3e-320 2.774673E-320
Double :     2.77467266704444059e-320
```

Now, we need to find the `_EPROCESS` of our target process. It's possible by using the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command.

```clike
1: kHyperDbg> .process list

...

PROCESS ffff948cc1517080
        Process Id: 23d0        DirBase (Kernel Cr3): 00000000127c0002  Image: dllhost.exe

PROCESS ffff948cc2393080
        Process Id: 15f0        DirBase (Kernel Cr3): 0000000190649002  Image: notepad.exe

PROCESS ffff948cc06f6080
        Process Id: 0fc4        DirBase (Kernel Cr3): 000000002902c002  Image: Taskmgr.exe
...
```

After that, we'll map the `_EPROCESS` of the **notepad.exe** to find the location of `SeAuditProcessCreationInfo`. As is evident, it's located at `+0x05c0` from the start of the `_EPROCESS`.

```clike
1: kHyperDbg> dt nt!_EPROCESS ffff948cc2393080
 _EPROCESS

...

  +0x05b7 uint8_t PriorityClass : 0x2
  +0x05b8 void* SecurityPort : (null)
  +0x05c0 _SE_AUDIT_PROCESS_CREATION_INFO SeAuditProcessCreationInfo : ffff948c`c25fa2a0
  +0x05c8 _LIST_ENTRY JobLinks : _LIST_ENTRY [ 00000000`00000000 - 00000000`00000000 ]
...
```

Now, we'll map it to `_SE_AUDIT_PROCESS_CREATION_INFO` to find the `ImageFileName`.

![](../../../../.gitbook/assets/Get-SeAuditProcessCreationInfo-data.PNG)

Before that, we want to convert the **notepad.exe** to **HyperDbg.exe**. For this purpose, we need to convert "**HyperDbg.exe**" to the hex format and add **00** to each character because this field is in Unicode format.

![](../../../../.gitbook/assets/hyperdbg-ascii-to-hex.PNG)

We read the pointer located at `_EPROCESS+0x05c0` and modify the **notepad.exe** in the target `_UNCODE_STRING` by using the '[eb](https://docs.hyperdbg.org/commands/debugging-commands/e)' command.

![](<../../../../.gitbook/assets/chagne-the-process-name (1).PNG>)

It's time to continue the debuggee and see the results.

![](../../../../.gitbook/assets/result-of-changed-process-name.PNG)

As it might be seen, it's changed to '**HyperDbg.ex**' because we didn't update the `Length` field of the `_UNICODE_STRING`. Changing this value is left as an exercise for the reader.

That's it. In this example, we saw how we could use the structure mapping commands in HyperDbg to change the process name in the Task Manager.

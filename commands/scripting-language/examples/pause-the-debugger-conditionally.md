---
description: >-
  An example of pausing system while a special path is passed to the
  NtCreateFile
---

# pause the debugger conditionally

In the [previous example](https://docs.hyperdbg.org/commands/scripting-language/examples/trace-function-calls), we saw how to create logs from [**nt!NtOpenFile**](https://docs.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-ntopenfile) function. Now, we want to explore [**nt!NtCreateFile**](https://learn.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-ntcreatefile).

{% hint style="info" %}
This example is written to be used in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), but you can run it on [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode). In the VMI Mode, you cannot [pause](https://docs.hyperdbg.org/commands/scripting-language/functions/debugger/pause) the system but you're still able to change the system control flow.
{% endhint %}

From the [MSDN](https://learn.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-ntcreatefile), **NtCreateFile** is defined like this:

```clike
__kernel_entry NTSTATUS NtCreateFile(
  [out]          PHANDLE            FileHandle,
  [in]           ACCESS_MASK        DesiredAccess,
  [in]           POBJECT_ATTRIBUTES ObjectAttributes,
  [out]          PIO_STATUS_BLOCK   IoStatusBlock,
  [in, optional] PLARGE_INTEGER     AllocationSize,
  [in]           ULONG              FileAttributes,
  [in]           ULONG              ShareAccess,
  [in]           ULONG              CreateDisposition,
  [in]           ULONG              CreateOptions,
  [in]           PVOID              EaBuffer,
  [in]           ULONG              EaLength
);
```

There is no pointer to the file name in the above prototype. The file name is embedded into the **ObjectAttributes** parameter to this function.

If you want to see how **OBJECT\_ATTRIBUTES** structure is defined, you can see [this](https://docs.microsoft.com/en-us/windows/win32/api/ntdef/ns-ntdef-\_object\_attributes) link from MSDN.

```c
typedef struct _OBJECT_ATTRIBUTES {
  ULONG           Length;
  HANDLE          RootDirectory;
  PUNICODE_STRING ObjectName;
  ULONG           Attributes;
  PVOID           SecurityDescriptor;
  PVOID           SecurityQualityOfService;
} OBJECT_ATTRIBUTES;
```

From the relative-address point of view, this function is stored in the memory like this:

```clike
   +0x000 Length           : Uint4B
   +0x008 RootDirectory    : Ptr64 Void
   +0x010 ObjectName       : Ptr64 _UNICODE_STRING
   +0x018 Attributes       : Uint4B
   +0x020 SecurityDescriptor : Ptr64 Void
   +0x028 SecurityQualityOfService : Ptr64 Void
```

We can see that there is a [**UNICODE\_STRING**](https://docs.microsoft.com/en-us/windows/win32/api/subauth/ns-subauth-unicode\_string) field named **ObjectName**. This is the name of the object that we're trying to open using **NtCreateFile**.

If we look at the **UNICODE\_STRING** structure. It's defined like this:

```clike
typedef struct _UNICODE_STRING {
  USHORT Length;
  USHORT MaximumLength;
  PWSTR  Buffer;
} UNICODE_STRING, *PUNICODE_STRING;
```

And the compiler saves it like this:

```clike
   +0x000 Length           : Uint2B
   +0x002 MaximumLength    : Uint2B
   +0x008 Buffer           : Ptr64 Wchar
```

Now, we have all the offsets that we want to observe the file names.

First, the **ObjectAttributes** parameter is passed as the **3rd** parameter to the function, and as the calling convention is Windows fastcall (**rcx**, **rdx**, **r8**, **r9**, **stack**), our target parameter is located at the **r8** register.

In our case, **r8** is a pointer to the **OBJECT\_ATTRIBUTES**, and if we add **0x10** to it, we'll reach the **ObjectName** field of this structure.

**ObjectName** is a pointer to the **UNICODE\_STRING**, so we'll dereference this pointer using the `poi` operator to reach the top of the **UNICODE\_STRING**.

In the **UNICODE\_STRING**, we'll add **0x8** to get the **Buffer** filed of this structure, and now we dereference it again to get the pointer where the file name string is located. The string is in wide-character format, so for the comparison, we need to use the [wcscmp](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcscmp) function.

At this point, we want to pause the debugger (halt the system) while Windows passes the `C:\folder\test.txt` file to the **NtCreateFile** in the kernel. We'll use the '[!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook)' command to execute a script on each execution of the **NtCreateFile** function, and then compare the file name with `\\??\\C:\\folder\\test.txt` and if it's equal to this file, then we [pause](https://docs.hyperdbg.org/commands/scripting-language/functions/debugger/pause) the debugger. Note that, `\\??` is added to the start of the file.

The final script is like this:

```clike
!epthook nt!NtCreateFile script {
	if(wcscmp(L"\\??\\C:\\folder\\test.txt",poi(poi(@r8+10)+8)) == 0){
		pause();
	}
}
```

To test it, you need to open a notepad and save the file at the target location (`C:\folder\test.txt`), and you'll see the debugger is paused.

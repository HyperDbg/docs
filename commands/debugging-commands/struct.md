---
description: Description of the 'struct' command in HyperDbg.
---

# struct (make structures, enums, data types from symbols)

### Command

> struct

### Syntax

> struct \[Module!SymbolName (string)] \[padding Padding (yesno)] \[offset Offset (yesno)] \[bitfield Bitfield (yesno)] \[native Native (yesno)] \[decl Declaration (yesno)] \[def Definitions (yesno)] \[func Functions (yesno)] \[pragma Pragma (yesno)] \[prefix Prefix (string)] \[suffix Suffix (string)] \[inline Expantion (string)] \[output FileName (string)]

### Description

Displays structures, enums, and data types in a **C** (header) format.

{% hint style="info" %}
You can use this command to create **C** (header) files from **all** of the symbols (structures, enums, data types) in the modules by using `module!*`. See [examples](https://docs.hyperdbg.org/commands/debugging-commands/struct#examples) for more information.
{% endhint %}

### Parameters

**\[Module!SymbolName (string)]**

Module name combined with the symbol name (separated by a `!` sign).

**\[padding Padding (yesno)] (optional)**

Create padding members. (default: **yes**)

**\[offset Offset (yesno)] (optional)**

Show offsets. (default: **yes**)

**\[bitfield Bitfield (yesno)] (optional)**

Allow bitfields in the union. (default: **no**)

**\[native Native (yesno)] (optional)**

Use types from stdint.h instead of native types. (default: **no**)

**\[decl Declaration (yesno)] (optional)**

Print declarations. (default: **yes**)

**\[def Definitions (yesno)] (optional)**

Print definitions. (default: **yes**)

**\[func Functions (yesno)] (optional)**

Print functions. (default: **no**)

**\[pragma Pragma (yesno)] (optional)**

Print #pragma pack directives. (default: **yes**)

**\[prefix Prefix (string)] (optional)**

Prefix for all symbols.

**\[suffix Suffix (string)] (optional)**

Suffix for all symbols.

**\[inline Expantion (string)] (optional)**

Specifies expansion of nested structures/unions. (default: **unnamed**)

&#x20;  **none**: only the top-most type is printed.

&#x20;  **unnamed**: unnamed types are nested.

&#x20;  **all**: all types are nested.

**\[output FileName (string)] (optional)**

Specifies the output file if the user wants to save the printed data.

### Examples

The following command is used to convert `nt!_TOKEN` into a C format code.

```clike
1: kHyperDbg> struct nt!_TOKEN
typedef struct _TOKEN
{
  /* 0x0000 */ struct _TOKEN_SOURCE TokenSource;
  /* 0x0010 */ struct _LUID TokenId;
  /* 0x0018 */ struct _LUID AuthenticationId;
  /* 0x0020 */ struct _LUID ParentTokenId;
  /* 0x0028 */ union _LARGE_INTEGER ExpirationTime;
  /* 0x0030 */ struct _ERESOURCE* TokenLock;
  /* 0x0038 */ struct _LUID ModifiedId;
  /* 0x0040 */ struct _SEP_TOKEN_PRIVILEGES Privileges;
  /* 0x0058 */ struct _SEP_AUDIT_POLICY AuditPolicy;
  /* 0x0077 */ char Padding_0;
  /* 0x0078 */ uint32_t SessionId;
  /* 0x007c */ uint32_t UserAndGroupCount;
  /* 0x0080 */ uint32_t RestrictedSidCount;
  /* 0x0084 */ uint32_t VariableLength;
  /* 0x0088 */ uint32_t DynamicCharged;
  /* 0x008c */ uint32_t DynamicAvailable;
  /* 0x0090 */ uint32_t DefaultOwnerIndex;
  /* 0x0094 */ long Padding_1;
  /* 0x0098 */ struct _SID_AND_ATTRIBUTES* UserAndGroups;
  /* 0x00a0 */ struct _SID_AND_ATTRIBUTES* RestrictedSids;
  /* 0x00a8 */ void* PrimaryGroup;
  /* 0x00b0 */ uint32_t* DynamicPart;
  /* 0x00b8 */ struct _ACL* DefaultDacl;
  /* 0x00c0 */ enum _TOKEN_TYPE TokenType;
  /* 0x00c4 */ enum _SECURITY_IMPERSONATION_LEVEL ImpersonationLevel;
  /* 0x00c8 */ uint32_t TokenFlags;
  /* 0x00cc */ uint8_t TokenInUse;
  /* 0x00cd */ char Padding_2[3];
  /* 0x00d0 */ uint32_t IntegrityLevelIndex;
  /* 0x00d4 */ uint32_t MandatoryPolicy;
  /* 0x00d8 */ struct _SEP_LOGON_SESSION_REFERENCES* LogonSession;
  /* 0x00e0 */ struct _LUID OriginatingLogonSession;
  /* 0x00e8 */ struct _SID_AND_ATTRIBUTES_HASH SidHash;
  /* 0x01f8 */ struct _SID_AND_ATTRIBUTES_HASH RestrictedSidHash;
  /* 0x0308 */ struct _AUTHZBASEP_SECURITY_ATTRIBUTES_INFORMATION* pSecurityAttributes;
  /* 0x0310 */ void* Package;
  /* 0x0318 */ struct _SID_AND_ATTRIBUTES* Capabilities;
  /* 0x0320 */ uint32_t CapabilityCount;
  /* 0x0324 */ long Padding_3;
  /* 0x0328 */ struct _SID_AND_ATTRIBUTES_HASH CapabilitiesHash;
  /* 0x0438 */ struct _SEP_LOWBOX_NUMBER_ENTRY* LowboxNumberEntry;
  /* 0x0440 */ struct _SEP_CACHED_HANDLES_ENTRY* LowboxHandlesEntry;
  /* 0x0448 */ struct _AUTHZBASEP_CLAIM_ATTRIBUTES_COLLECTION* pClaimAttributes;
  /* 0x0450 */ void* TrustLevelSid;
  /* 0x0458 */ struct _TOKEN* TrustLinkedToken;
  /* 0x0460 */ void* IntegrityLevelSidValue;
  /* 0x0468 */ struct _SEP_SID_VALUES_BLOCK* TokenSidValues;
  /* 0x0470 */ struct _SEP_LUID_TO_INDEX_MAP_ENTRY* IndexEntry;
  /* 0x0478 */ struct _SEP_TOKEN_DIAG_TRACK_ENTRY* DiagnosticInfo;
  /* 0x0480 */ struct _SEP_CACHED_HANDLES_ENTRY* BnoIsolationHandlesEntry;
  /* 0x0488 */ void* SessionObject;
  /* 0x0490 */ uint64_t VariablePart;
} TOKEN, *PTOKEN; /* size: 0x0498 */
```

You can aslo use this command to rebuild enums.

```clike
HyperDbg> struct nt!POWER_ACTION
enum POWER_ACTION
{
  PowerActionNone = 0,
  PowerActionReserved = 1,
  PowerActionSleep = 2,
  PowerActionHibernate = 3,
  PowerActionShutdown = 4,
  PowerActionShutdownReset = 5,
  PowerActionShutdownOff = 6,
  PowerActionWarmEject = 7,
  PowerActionDisplayOff = 8,
};
```

It's possible to dump all of the structures, enums, and data types into a header file.

```
1: kHyperDbg> struct nt!* output NtHeader.h
```

The following command is used to inline each structure into the parent structure.

```clike
HyperDbg> struct nt!_SID inline all
typedef struct _SID
{
  /* 0x0000 */ unsigned char Revision;
  /* 0x0001 */ unsigned char SubAuthorityCount;
  struct _SID_IDENTIFIER_AUTHORITY
  {
    /* 0x0002 */ unsigned char Value[6];
  } /* size: 0x0006 */ IdentifierAuthority;
  /* 0x0008 */ unsigned long SubAuthority[1];
} SID, *PSID; /* size: 0x000c */
```

The following command is used to recursively dump the `nt!_TOKEN` structure and its sub-structures.

```clike
1: kHyperDbg> struct nt!_TOKEN def yes
#include <pshpack1.h>
typedef struct _LUID
{
  /* 0x0000 */ unsigned long LowPart;
  /* 0x0004 */ long HighPart;
} LUID, *PLUID; /* size: 0x0008 */

typedef struct _TOKEN_SOURCE
{
  /* 0x0000 */ char SourceName[8];
  /* 0x0008 */ struct _LUID SourceIdentifier;
} TOKEN_SOURCE, *PTOKEN_SOURCE; /* size: 0x0010 */

typedef union _LARGE_INTEGER
{
  union
  {
    struct
    {
      /* 0x0000 */ unsigned long LowPart;
      /* 0x0004 */ long HighPart;
    }; /* size: 0x0008 */
    struct
    {
      /* 0x0000 */ unsigned long LowPart;
      /* 0x0004 */ long HighPart;
    } /* size: 0x0008 */ u;
    /* 0x0000 */ __int64 QuadPart;
  }; /* size: 0x0008 */
} LARGE_INTEGER, *PLARGE_INTEGER; /* size: 0x0008 */

typedef struct _SEP_TOKEN_PRIVILEGES
{
  /* 0x0000 */ unsigned __int64 Present;
  /* 0x0008 */ unsigned __int64 Enabled;
  /* 0x0010 */ unsigned __int64 EnabledByDefault;
} SEP_TOKEN_PRIVILEGES, *PSEP_TOKEN_PRIVILEGES; /* size: 0x0018 */

typedef struct _TOKEN_AUDIT_POLICY
{
  /* 0x0000 */ unsigned char PerUserPolicy[30];
} TOKEN_AUDIT_POLICY, *PTOKEN_AUDIT_POLICY; /* size: 0x001e */

typedef struct _SEP_AUDIT_POLICY
{
  /* 0x0000 */ struct _TOKEN_AUDIT_POLICY AdtTokenPolicy;
  /* 0x001e */ unsigned char PolicySetStatus;
} SEP_AUDIT_POLICY, *PSEP_AUDIT_POLICY; /* size: 0x001f */

typedef enum _TOKEN_TYPE
{
  TokenPrimary = 1,
  TokenImpersonation = 2,
} TOKEN_TYPE, *PTOKEN_TYPE;

typedef enum _SECURITY_IMPERSONATION_LEVEL
{
  SecurityAnonymous = 0,
  SecurityIdentification = 1,
  SecurityImpersonation = 2,
  SecurityDelegation = 3,
} SECURITY_IMPERSONATION_LEVEL, *PSECURITY_IMPERSONATION_LEVEL;

typedef struct _SID_AND_ATTRIBUTES_HASH
{
  /* 0x0000 */ unsigned long SidCount;
  /* 0x0004 */ long Padding_0;
  /* 0x0008 */ struct _SID_AND_ATTRIBUTES* SidAttr;
  /* 0x0010 */ unsigned __int64 Hash[32];
} SID_AND_ATTRIBUTES_HASH, *PSID_AND_ATTRIBUTES_HASH; /* size: 0x0110 */

typedef struct _TOKEN
{
  /* 0x0000 */ struct _TOKEN_SOURCE TokenSource;
  /* 0x0010 */ struct _LUID TokenId;
  /* 0x0018 */ struct _LUID AuthenticationId;
  /* 0x0020 */ struct _LUID ParentTokenId;
  /* 0x0028 */ union _LARGE_INTEGER ExpirationTime;
  /* 0x0030 */ struct _ERESOURCE* TokenLock;
  /* 0x0038 */ struct _LUID ModifiedId;
  /* 0x0040 */ struct _SEP_TOKEN_PRIVILEGES Privileges;
  /* 0x0058 */ struct _SEP_AUDIT_POLICY AuditPolicy;
  /* 0x0077 */ char Padding_1;
  /* 0x0078 */ unsigned long SessionId;
  /* 0x007c */ unsigned long UserAndGroupCount;
  /* 0x0080 */ unsigned long RestrictedSidCount;
  /* 0x0084 */ unsigned long VariableLength;
  /* 0x0088 */ unsigned long DynamicCharged;
  /* 0x008c */ unsigned long DynamicAvailable;
  /* 0x0090 */ unsigned long DefaultOwnerIndex;
  /* 0x0094 */ long Padding_2;
  /* 0x0098 */ struct _SID_AND_ATTRIBUTES* UserAndGroups;
  /* 0x00a0 */ struct _SID_AND_ATTRIBUTES* RestrictedSids;
  /* 0x00a8 */ void* PrimaryGroup;
  /* 0x00b0 */ unsigned long* DynamicPart;
  /* 0x00b8 */ struct _ACL* DefaultDacl;
  /* 0x00c0 */ enum _TOKEN_TYPE TokenType;
  /* 0x00c4 */ enum _SECURITY_IMPERSONATION_LEVEL ImpersonationLevel;
  /* 0x00c8 */ unsigned long TokenFlags;
  /* 0x00cc */ unsigned char TokenInUse;
  /* 0x00cd */ char Padding_3[3];
  /* 0x00d0 */ unsigned long IntegrityLevelIndex;
  /* 0x00d4 */ unsigned long MandatoryPolicy;
  /* 0x00d8 */ struct _SEP_LOGON_SESSION_REFERENCES* LogonSession;
  /* 0x00e0 */ struct _LUID OriginatingLogonSession;
  /* 0x00e8 */ struct _SID_AND_ATTRIBUTES_HASH SidHash;
  /* 0x01f8 */ struct _SID_AND_ATTRIBUTES_HASH RestrictedSidHash;
  /* 0x0308 */ struct _AUTHZBASEP_SECURITY_ATTRIBUTES_INFORMATION* pSecurityAttributes;
  /* 0x0310 */ void* Package;
  /* 0x0318 */ struct _SID_AND_ATTRIBUTES* Capabilities;
  /* 0x0320 */ unsigned long CapabilityCount;
  /* 0x0324 */ long Padding_4;
  /* 0x0328 */ struct _SID_AND_ATTRIBUTES_HASH CapabilitiesHash;
  /* 0x0438 */ struct _SEP_LOWBOX_NUMBER_ENTRY* LowboxNumberEntry;
  /* 0x0440 */ struct _SEP_CACHED_HANDLES_ENTRY* LowboxHandlesEntry;
  /* 0x0448 */ struct _AUTHZBASEP_CLAIM_ATTRIBUTES_COLLECTION* pClaimAttributes;
  /* 0x0450 */ void* TrustLevelSid;
  /* 0x0458 */ struct _TOKEN* TrustLinkedToken;
  /* 0x0460 */ void* IntegrityLevelSidValue;
  /* 0x0468 */ struct _SEP_SID_VALUES_BLOCK* TokenSidValues;
  /* 0x0470 */ struct _SEP_LUID_TO_INDEX_MAP_ENTRY* IndexEntry;
  /* 0x0478 */ struct _SEP_TOKEN_DIAG_TRACK_ENTRY* DiagnosticInfo;
  /* 0x0480 */ struct _SEP_CACHED_HANDLES_ENTRY* BnoIsolationHandlesEntry;
  /* 0x0488 */ void* SessionObject;
  /* 0x0490 */ unsigned __int64 VariablePart;
} TOKEN, *PTOKEN; /* size: 0x0498 */

#include <poppack.h>
```

### IOCTL

None

### Remarks

{% hint style="info" %}
For implementing this command, [**pdbex**](https://github.com/wbenny/pdbex) is integrated into HyperDbg.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[dt (display and map virtual memory to structures)](https://docs.hyperdbg.org/commands/debugging-commands/dt)

[!dt (display and map physical memory to structures)](https://docs.hyperdbg.org/commands/extension-commands/dt)

[Mapping Data & Create Structures, and Enums From Symbols](https://docs.hyperdbg.org/using-hyperdbg/kernel-mode-debugging/examples/basics/mapping-data-and-create-structures-and-enums-from-symbols)

---
description: Configuring symbol server and path
---

# Configuring Symbol Server/Path

After you connect to HyperDbg, it's highly recommended to configure the symbol server to have a better debugging experience.

For this purpose, first, we have to configure the symbol server address or local symbol path using the '[.sympath](https://docs.hyperdbg.org/commands/meta-commands/.sympath)' command. After that, we can use the '[.sym](https://docs.hyperdbg.org/commands/meta-commands/.sym)' command to load or download symbols.

Firstly, we need a symbol path. We often set a local symbol path combined with a remote symbol server (e.g., Microsoft Symbol Server). For example, the following path is used:

`SRV*c:\symbols*https://msdl.microsoft.com/download/symbols`

In the above path, first, it checks if the symbol exists in `c:\symbols`. If not, then it checks `https://msdl.microsoft.com/download/symbols`.

All the paths strings should be started with `SRV*`.

Now, the following command sets the symbol path to `SRV*c:\symbols*https://msdl.microsoft.com/download/symbols`.

```
HyperDbg> .sympath SRV*c:\Symbols*https://msdl.microsoft.com/download/symbols
```

And the following command shows the current symbol server.

```
HyperDbg> .sympath
current SymbolServer is : SRV*c:\Symbols*https://msdl.microsoft.com/download/symbols
```

The next step is to update the symbol table.

Use the following command to update the symbol table and all the symbols from the local symbol path (and **NOT download** them).

```c
HyperDbg> .sym reload
symbol table updated successfully
```

If you want to load all the symbols from the local symbol path and, if not available then, download them from the remote symbol server (e.g., Microsoft Symbol Server), use the following command.

```c
HyperDbg> .sym download
downloading symbol 'ntkrnlmp.pdb'...    downloaded
downloading symbol 'kd.pdb'...  downloaded
downloading symbol 'mcupdate_GenuineIntel.pdb'...       downloaded
downloading symbol 'clfs.pdb'...        downloaded
downloading symbol 'tm.pdb'...  downloaded
downloading symbol 'pshed.pdb'...       downloaded
downloading symbol 'bootvid.pdb'...     downloaded
downloading symbol 'fltMgr.pdb'...      downloaded
downloading symbol 'msrpc.pdb'...       downloaded
downloading symbol 'ksecdd.pdb'...      downloaded
downloading symbol 'clipsp.pdb'...      downloaded
downloading symbol 'cmimcext.pdb'...    downloaded
downloading symbol 'WerKernel.pdb'...   downloaded
downloading symbol 'ntosext.pdb'...     downloaded
downloading symbol 'ci.pdb'...  downloaded
downloading symbol 'cng.pdb'... downloaded
downloading symbol 'Wdf01000.pdb'...    downloaded
downloading symbol 'wdfldr.pdb'...      downloaded
downloading symbol 'wpprecorder.pdb'... downloaded
downloading symbol 'SleepStudyHelper.pdb'...    downloaded
downloading symbol 'acpiex.pdb'...      downloaded
downloading symbol 'mssecflt.pdb'...    downloaded
downloading symbol 'SgrmAgent.pdb'...   downloaded
downloading symbol 'acpi.pdb'...        downloaded
downloading symbol 'wmilib.pdb'...      downloaded
downloading symbol 'intelpep.pdb'...    downloaded
downloading symbol 'WindowsTrustedRT.pdb'...

...
```

After that, each time you load HyperDbg in both local debugging ([VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode)) and remote serial debugging ([Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode)), HyperDbg loads the previously downloaded modules from the path (it **won't** download symbols from the remote server without your permission, you should use `.sym download` to download the symbols from the Internet. However, local symbols are automatically loaded).

When you finish configuring symbols, you can use the function names, variables, and object names in the debugger commands.

# conditional breakpoints

Conditional statements are one of the essential parts of debuggers, and HyperDbg is not an exception.

These statements are handled in both command syntax, like you can specify a **core **or **pid **that triggers the event. Still, you can also use the script engine that gives you more flexibility to compare different arguments and, if necessary, call functions.

In this example, we want to put a conditional breakpoint on `nt!ExAllocatePoolWithTag` only and only when it's called from `nt!CmpAllocatePoolWithTag` function.

Let's take a look at memory at `nt!ExAllocatePoolWithTag` :

```
2: kHyperDbg> u nt!ExAllocatePoolWithTag
fffff801`639b1030    48 89 5C 24 08                      mov qword ptr ss:[rsp+0x08], rbx
fffff801`639b1035    48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff801`639b103a    48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff801`639b103f    57                                  push rdi
fffff801`639b1040    41 56                               push r14
fffff801`639b1042    41 57                               push r15
fffff801`639b1044    48 83 EC 30                         sub rsp, 0x30
fffff801`639b1048    65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff801`639b1051    45 8B F0                            mov r14d, r8d
fffff801`639b1054    44 0F B7 3D A4 8F 34 00             movzx r15d, word ptr ds:[0xFFFFF80163CFA000]
fffff801`639b105c    48 8B EA                            mov rbp, rdx
```

The assebly code for `nt!CmpAllocatePoolWithTag` :

```
2: kHyperDbg> u nt!CmpAllocatePoolWithTag
fffff801`6325c5c0    48 83 EC 28                         sub rsp, 0x28
fffff801`6325c5c4    E8 67 4A 75 00                      call 0xFFFFF801639B1030
fffff801`6325c5c9    48 83 C4 28                         add rsp, 0x28
fffff801`6325c5cd    C3                                  ret
fffff801`6325c5ce    CC                                  int3
fffff801`6325c5cf    CC                                  int3
fffff801`6325c5d0    CC                                  int3

```

As you can see, `nt!ExAllocatePoolWithTag` is called from ``fffff801`6325c5c4`` in `nt!CmpAllocatePoolWithTag`.

Based on x86 assembly, a `call` instruction pushes the address of the next instruction to the stack, so that the program can be continued later with the next instruction.

As the stack is down-to-up in computer science, if we dereference **@rsp** register and read 8-bytes from the stack, it's the pointer to the next instruction that the caller expects to be called after the call is finished (returned).

We know that the next instruction after the call instruction is ``fffff801`6325c5c9`` which is `nt!CmpAllocatePoolWithTag+0x9`. Now, we put a EPT hook on the target function `nt!ExAllocatePoolWithTag` and check whether the caller is `nt!CmpAllocatePoolWithTag+0x9`.

If the caller is our what we expected, then we'll halt the debugger and get the control of the debuggee by using [pause();](https://docs.hyperdbg.org/commands/scripting-language/functions/debugger/pause) function.

All in all, the following script is the implementation of this logic.

```
!epthook nt!ExAllocatePoolWithTag script {

	if (poi(@rsp) == nt!CmpAllocatePoolWithTag+0x9) {
		pause();
	}
}
```

You can see that the debugger will get the target system's control whenever it's called from `nt!CmpAllocatePoolWithTag+0x4`.

![](broken-reference)

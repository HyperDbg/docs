---
description: 'Using d*, e*, and s* commands'
---

# Displaying & Editing & Searching Memory

One of the debugger's essential tasks is displaying or editing memory, and HyperDbg is not an exception.

You can display, edit, and search both physical and virtual addresses using the following commands.

### Displaying Memory

The following command is used when we want to read the content of memory at **``fffff800`3ad6f010``** with length of `0x50` from the memory layout view of process \(`4` a.k.a. system process\) in a hex byte format.

```diff
HyperDbg> db fffff800`3ad6f010 l 50 pid 4
fffff800`3ad6f010  48 89 5C 24 08 48 89 6C 24 10 48 89 74 24 18 57  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  41 56 41 57 48 83 EC 30 65 48 8B 04 25 20 00 00  AVAWH..0eH.. ..
fffff800`3ad6f030  00 33 DB 44 0F B7 3D C5 3F 20 00 41 8B E8 48 8B  .3.D..=.? .A..H.
fffff800`3ad6f040  F2 89 5C 24 68 8B F9 4C 8B 88 C0 00 00 00 45 0F  ..\$h..L......E.
fffff800`3ad6f050  B7 B1 92 00 00 00 41 8B C6 44 8B C8 89 5C 24 20  ......A..D...\$
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word value \(4 bytes\) and ASCII characters format.

```diff
HyperDbg> dc fffff800`3ad6f010
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  57415641 30EC8348 048B4865 00002025  AVAWH..0eH.. ..
fffff800`3ad6f030  44DB3300 C53DB70F 4100203F 8B48E88B  .3.D..=.? .A..H.
fffff800`3ad6f040  245C89F2 4CF98B68 00C0888B 0F450000  ..\$h..L......E.
fffff800`3ad6f050  0092B1B7 8B410000 C88B44C6 20245C89  ......A..D...\$
fffff800`3ad6f060  48C58B44 CF8BD68B CC3693E8 C08548FF  D..H......6..H..
fffff800`3ad6f070  0038840F 8B480000 6C8B48D8 8B485824  ..8...H..H.l$XH.
fffff800`3ad6f080  5C8B48C3 8B485024 48602474 4130C483  .H.\$PH.t$`H..0A
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word values \(4 bytes\) format with the length of `0x10`.

```diff
HyperDbg> dd fffff800`3ad6f010 l 10
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Quad-word values \(8 bytes\) format.

```diff
HyperDbg> dq fffff800`3ad6f010
fffff800`3ad6f010  6C894808`245C8948 57182474`89481024
fffff800`3ad6f020  30EC8348`57415641 00002025`048B4865
fffff800`3ad6f030  C53DB70F`44DB3300 8B48E88B`4100203F
fffff800`3ad6f040  4CF98B68`245C89F2 0F450000`00C0888B
fffff800`3ad6f050  8B410000`0092B1B7 20245C89`C88B44C6
fffff800`3ad6f060  CF8BD68B`48C58B44 C08548FF`CC3693E8
fffff800`3ad6f070  8B480000`0038840F 8B485824`6C8B48D8
fffff800`3ad6f080  8B485024`5C8B48C3 4130C483`48602474
```

### Editing Memory

The following command is used when we want to edit the content of memory at ``fffff800`3ad6f010`` in a hex byte form and change it to `0x90 0x90 0x90` \(modify **three** bytes\).

```diff
HyperDbg> eb fffff800`3ad6f010 90 90 90
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Double-word values \(4 bytes\), change it to `245C8948` .

```diff
HyperDbg> ed fffff800`3ad6f010 245C8948
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Quad-word values \(8 bytes\), change it to ``88889898`85858686``  and``92929393`97979898`` \(16 bytes\).

```diff
HyperDbg> eq fffff800`3ad6f010 88889898`85858686 92929393`97979898
```

### Searching Memory

The following command is used to search for `4156415748` starting from ``fffff807`7356f010`` to ``fffff807`7356f010+ffff``.

```diff
HyperDbg> sb fffff807`7356f010 l ffff 41 56 41 57 48 
```

The following example is used when we want to search for `f0cc8549` from `7FF62C9016AD` to `7FF62C9016AD+fff` in a different process \(process id = `1dd0`\).

```diff
HyperDbg> sd 7FF62C9016AD pid 1dd0 l fff f0cc8549 
```

The following example is used when we want to search for ``0f450000`00c0888b`` ``8b410000`0092b1b7`` from ``fffff807`7356f010`` to ``fffff807`7356f010+100``.

```diff
HyperDbg> sq fffff807`7356f010 l 100 0f450000`00c0888b 8b410000`0092b1b7
```

{% hint style="info" %}
If you want to perform the above actions on physical addresses, you can add **`!`** to any of the above commands.
{% endhint %}


問題： 解釋在bootloader轉換到kernel時的esp(x /24x $esp)

```
(gdb) x /24x $esp
0x7bdc:	0x00007db4	0x00000000	0x00000000	0x00000000
0x7bec:	0x00000000	0x00000000	0x00000000	0x00000000
0x7bfc:	0x00007c4d	0x8ec031fa	0x8ec08ed8	0xa864e4d0
0x7c0c:	0xb0fa7502	0xe464e6d1	0x7502a864	0xe6dfb0fa
0x7c1c:	0x16010f60	0x200f7c78	0xc88366c0	0xc0220f01
0x7c2c:	0x087c31ea	0x10b86600	0x8ed88e00	0x66d08ec0
```

bios將bootloader加載到0x7c00
直到要執行bootmain之前：

```
0x7c43: movl    $start, %esp
0x7c48: call    bootmain
--- equal to ---
0x7c43: mov     $0x7c00,%esp
0x7c48: call    7d3b <bootmain>
```

故esp會先指向0x7c00, 然後call bootmain,
這時會將return address入棧並且esp-4, 並從0x7d3b開始執行,
因此0x7bfc會存0x7c4d(return address), 而0x7c00前的則毫無疑問就是bootloader

從7d3b開始：
```
7d3b:	55                      push   %ebp
7d3c:	89 e5                   mov    %esp,%ebp
7d3e:	57                      push   %edi
7d3f:	56                      push   %esi
7d40:	53                      push   %ebx
7d41:	83 ec 0c                sub    $0xc,%esp
```
4個push和esp-12, 因此一共減少28words,
因此從0x7bfc之後開始會存入7個0x00000000,
然後停在0x7be0, 而在bootmain最後交給kernel時:
```
7dae:	ff 15 18 00 01 00       call   *0x10018
```
會將esp-4, returun address入棧
因此在0x7bdc會存入下一條指令：0x7db4

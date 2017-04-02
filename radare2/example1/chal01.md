### Puzzle chal01:

#### Quick tips:

##### Commands: 
* dr?rax : Get value of register rax

##### Theory:
* eax,ebx,ecx,edx : general purpose registers (the e letter means extended)
* eax register is 32bits, you can access parts of that register using ax, ah and al.
  Same applies to ebx, ecx and edx.
  Here's is a tabular representation of those registers:

```
    <--------16bits------><----8bit----><---8bit--->
    +---------------------------------------------+ _
eax | ax                 |    ah      |     al    |  | <-- Register a extended
    +---------------------------------------------+  |
ebx | bx                 |    bh      |     bl    |  | <-- Register b extended
    +---------------------------------------------+  | 
ecx | cx                 |    ch      |     cl    |  |
    +---------------------------------------------+  |
edx | dx                 |    dh      |     dl    |   > General purpose registers
    +---------------------------------------------+  |
rsi |                                             |  | <-- Source for data copies
    +---------------------------------------------+  |
rdi |                                             |  | <-- Destination for data copies
    +---------------------------------------------+ _|
rsp |                                             |    <-- Current stack pointer
    +---------------------------------------------+
rbp |                                             |    <-- Start stack pointer
    +---------------------------------------------+
```


### Reference:
* http://www.hep.wisc.edu/~pinghc/x86AssmTutorial.htm
* http://www.cs.virginia.edu/~evans/cs216/guides/x86.html
* https://wiki.cdot.senecacollege.ca/wiki/X86_64_Register_and_Instruction_Quick_Start

* Comments are marked with a # (e.g. # Comment here)

### Try #1 Check if the length of the input is important
```
radare2 -d chal01 0123456789
> aaa                   # Analyze all
> V                     # Visual mode
> [press p twice]       # Change visual mode
> db 0x004005f9         # Debug break point
> dc                    # Debug continue
> dr?rax                # Get value of rax register
0x0000000a
> [at address 0x004005f9: 0xa != 0x13] # 10 != 19, our input length is 10 and the length of the expected input is 19
```
Fail! Therefore the input length is important, it should be 19 or 0x13 in hex.

### Try #2 Check if the `-` are important
```
radare2 -d chal01 1111111111111111111
> aaa
> V
> [press p twice]
> db 0x0040060e
> dc
> dr?eax
0x00000031
> [at address 0x0040060e: 0x31 != 0x2d] # register al is the lowest 8-bits of eax register. So 0x31 != 0x2d or in other words, `1` is not `-`
```
Fail! Therefore the first `-` is important.

---

```
radare2 -d chal01 1111-11111111111111
> aaa
> V
> [press p twice]
> db 0x00400627
> dc
> dr?eax
0x00000031
> [at address 0x00400627: 0x31 != 0x2d] # `1` is not `-`
```
Fail! Therefore the second `-` is important.

---

```
radare2 -d chal01 1111-1111-111111111
> aaa
> V
> [press p twice]
> db 0x0040064f
> dc
> dr?eax
0x00000031
> [at address 0x0040064f: 0x31 != 0x2d] # `1` is not `-`
```
Fail! Therefore the third `-` is important.

### Try #3 Find the codes
```
radare2 -d chal01 1111-1111-1111-1111
> aaa
> V
> [press p twice]
> db 0x00400753 # Debug at cmp edx, eax
> dc
> dr?edx
0x00000006
> dr?eax
0x0000000a
```
Fail! The first character of the input (`1`) resulted in a `0x06` and the expected value is `0x0a`. Let's try another character.

---

```
radare2 -d chal01 P111-1111-1111-1111
> aaa
> V
> [press p twice]
> db 0x00400753 # Debug at cmp edx, eax
> dc
> dr?edx
0x0000000a
> dr?eax
0x0000000a
```
Hit! Just found the first character. Some other characters like `Q` and `R` will also result in `0x0a` which allows us to find more than one accepted key at the end.

Let's find the second character, continue the last debugging session
```
> dc
> dr?edx
0x00000018
> dr?eax
0x00000024
```
Fail! The second character didn't match.

```
radare2 -d chal01 PI1-1111-1111-1111
> aaa
> V
> [press p twice]
> db 0x00400753 # Debug at cmp edx, eax
> dc
> dc
> dr?edx
0x00000024
> dr?eax
0x00000024
```
Hit! Just found the second character. 

Keep applying this process until all 16 characters are found!

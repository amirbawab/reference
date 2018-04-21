# GDB reference sheet

## x86 memory
### Big image
```
0xfff
+------------+
|            | < Grow down
|    Stack   | < Push backward 
|            | < (decreasing addr)
|vvvvvvvvvvvv|
|            |
|^^^^^^^^^^^^|
|            |
|    Heap    | < Grow up
|            |
+------------+
|    Code    |
+------------+
0x000
```

### Stack buffer
```
+------------+
|   .......  | <
+------------+ <
|   ......   | < // char buf[256]
|   ......   | < Buffer index are 
|   buf[1]   | < opposite to stack
|   buf[0]   | < growth direction
+------------+
/!\ | Buffer overflow can overwrite previous stack
    | information
```

## x64/x86 Registers (inline for grep)
*x64 is compatible with x86 processor family but supports 64-bit instruction set*

```
%rax [(64)], %eax [32:(32)], %ax [48:(16)], %ah  [46:(8):8], %al  [46:8:(8)]
%rcx [(64)], %ecx [32:(32)], %cx [48:(16)], %ch  [46:(8):8], %cl  [46:8:(8)]
%rdx [(64)], %edx [32:(32)], %dx [48:(16)], %dh  [46:(8):8], %dl  [46:8:(8)]
%rbx [(64)], %ebx [32:(32)], %bx [48:(16)], %bh  [46:(8):8], %bl  [46:8:(8)]
%rsi [(64)], %esi [32:(32)], %si [48:(16)], %sih [46:(8):8], %sil [46:8:(8)] # Source index
%rdi [(64)], %edi [32:(32)], %di [48:(16)], %dih [46:(8):8], %dil [46:8:(8)] # Destination index
%rsp [(64)], %esp [32:(32)], %sp [48:(16)], %sph [46:(8):8], %spl [46:8:(8)] # Stack pointer
%rbp [(64)], %ebp [32:(32)], %bp [48:(16)], %bph [46:(8):8], %bpl [46:8:(8)] # Base pointer
%rip [64-bit cpu], %eip [32-bit cpu], %ip [16-bit cpu] # Instruction pointer, program counter
%r8, %r9, %r10, %r11, %r12, %r13, %r14, %r15 [64-bit]  # For x64
```

## x86 instructions (inline for grep)
```
mov eax,fffh # Store 0xfff into eax
mov ecx,edx  # Store val in edx into ecx
add eax,edx # eax += edx
add esi,11b # esi += 3
sub eax,edx # eax -= edx
sub esi,11b # esi -= 3
lea eax, [eax+1]        # eax = eax + 1
lea eax, [eax+2*edx]    # eax = eax + 2 * edx
push eax    # Push eax val into stack (updates e.g. rsp - 4)
pop eax     # Stores and pop top of stack into eax (updates e.g. rsp + 4)
```

## GDB commands (inline for grep)
```
p/x $eax      # Print value stored in register eax in hex format
p/d my_var    # Print value stored in variable my_var in dec format
x/5i $rip     # Examine content of the 5 memory addr starting from addr stored at $rip, in instruction format
disassemble func1    # Disassemble function func1
x/i $pc     # Examine (instruction format) at program counter
x/i $rip    # Examine (instruction format) at program counter (64-bit)
x/i $eip    # Examine (instruction format) at program counter (32-bit)
info registers  # Show all registers with their contents
info frame      # Show information about current frame
info locals     # Show local variables in current scope
```

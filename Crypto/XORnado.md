# XORnado – (Crypto)

## Challenge Description
> *"Something is happening again and again..... and again"*  
Category: Cryptography  
Idea: XOR Decryption  

We are given a binary and asked to recover the correct password/flag. The hint suggests a repeating operation → most likely **XOR with a repeating key**.

---

## Binary Analysis
Decompiling the binary in **Binary Ninja** revealed the following key code section:

```c
__builtin_strncpy(&var_4e, "womp", 6);

while (var_5c < strlen(rax_2)) {
    int32_t temp0_1 = HIGHD(var_5c);
    int32_t temp1_1 = LOWD(var_5c);
    uint32_t rdx_2 = temp0_1 >> 0x1e;
    rax_2[var_5c] ^= *(&var_4e + ((temp1_1 + rdx_2) & 3));
    var_5c += 1;
}
```

### Key observations:

The string "womp" is copied into memory (var_4e).
In the loop, every character of the input is XORed with a character from "womp".
The expression & 3 ensures that the key repeats in cycles of 4.

This confirms the encryption scheme is repeating-key XOR with key = "womp".


### Approach

Identify encryption: Decompiled binary showed XOR with key "womp".
Confirm repetition: Key repeats every 4 bytes.
Decrypt ciphertext: XOR the given ciphertext against "womp" to reveal the plaintext/flag.

### Solution Script

1. A simple Python script can recover the plaintext:
```python
cipher = bytes.fromhex("PUT_CIPHERTEXT_HERE")  # Replace with given data
key = b"womp"

plain = bytes([c ^ key[i % len(key)] for i, c in enumerate(cipher)])
print(plain.decode(errors="ignore"))
```

2. Alternatively, CyberChef can be used:
Load the ciphertext
Apply "From Hex"
Apply "XOR" with key = womp (set to repeat)

### Final Flag
> flag{x0r_repeats_until_you_stop_it}

### Lessons Learned

XOR with repeating keys is a classic CTF crypto challenge.

Binary analysis tools like Binary Ninja or Ghidra make it easier to extract the key.

Once the key is known, decryption is straightforward with scripting or CyberChef.

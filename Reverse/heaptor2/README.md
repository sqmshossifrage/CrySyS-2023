Running the binary and it seems like some hash function. Load in Ghidra and track symbols `main`, `algorithm`, `heaproutine_load` and then `transform`. It seems it has decoded `protected_algorithm` into instructions. We first extract `protected_algorithm` by selecting the data and `[Right Click] -> Copy Special... -> Python Byte String` and decode it offline. 

```python
x = 0x42
instr = b''
for i in data:
    i = i^x
    instr += i.to_bytes(1, 'little')
    x = i
instr = instr[:0x3d2] + b'\xea\xb9' + instr[0x3d4:]
```
Then we save `instr` into a file and open the file with Ghidra using `x86-64 Intel gcc` to disassemble the instructions. It seems the program is a bitwise shuffling of a 2048-bit string based on the offset of the bits. Figure out how it shuffles and apply the inverse of the mapping to the `FLAG`. In the end, we get some random bytes followed by a flag. 

Note: endianness matters! If it does not work, try to inverse something and try again. 
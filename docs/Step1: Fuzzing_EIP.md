# Running the program

With the following command, we launch the vulnerable program inside GDB (GNU Debugger): 
```bash
gdb -q /path/binary_name  # (The starting banner is suppressed for cleaner output with the -q option.)

```

---

# Sending the input

By sending an oversized input (A long sequence of characters or numbers), we observed when the instruction pointer EIP was overwritten.

<img width="1277" height="284" alt="image" src="https://github.com/user-attachments/assets/440a8efa-17cc-4169-9f4f-45e58770b2d8" />



For this purpose, we use the command: 
```bash
r $(python3 -c "import sys; sys.stdout.buffer.write(b'E' * 500)")

'''
This invocation writes exactly 500 bytes of the character E to stdout, which are then passed as input.
Now let us examine the register information to verify if our input reached the EIP.
'''
```

---

# Checking EIP and the registers

-- If you are using gdb without gef you can use:

```bash
info registers  # Displays the registers information
info registers $eip  # Just to display this register information 
```
<img width="1281" height="767" alt="image" src="https://github.com/user-attachments/assets/ac6c62ac-5cff-4c19-ae1c-aea88faeade0" />



As can be seen, we overwrite the EIP register with the provided input. Step 1 has been completed successfully.

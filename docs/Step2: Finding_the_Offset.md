## Creatting the pattern

To ensure precision in the process, a unique pattern was generated and provided as input using tools such as pattern_create from the Metasploit framework or cyclic from pwntools:

<img width="1277" height="761" alt="image" src="https://github.com/user-attachments/assets/6f409c2f-c7de-4ca6-8900-8ac11fb45737" />

(With GEF / Without GEF)  

The precise offset at which the EIP register is rewritten can be found using this pattern. Comparing EIP value against this pattern.

When we overwrite EIP for the first time, the number of bytes we utilize defines the -l flag, which indicates the pattern's length.

---

## Sending the pattern

We must now supply the program with the pattern as input. 

<img width="1279" height="212" alt="image" src="https://github.com/user-attachments/assets/8764b56e-3c56-4333-aa9f-c7eb53717763" />



(With GEF / Without GEF)

```bash
r $(python3 -c "import sys; sys.stdout.buffer.write(b'pattern')")  # Syntax
```

This time, when the program crashed we see a different value on the EIP register. 

<img width="1279" height="767" alt="image" src="https://github.com/user-attachments/assets/f4ee6807-7093-41d8-8015-9c93c3da3eaa" />


(With GEF / Without GEF)

---

## Locating the offset

Now, using the pattern_offset tool from metasploit framwework we are going to locate the exact amount of bytes needed to reach EIP with the command displayed on the image below (with gef and without )

<img width="1268" height="110" alt="image" src="https://github.com/user-attachments/assets/b72b33c2-5af1-4206-933b-7c608c754543" />

(With GEF / Without GEF)

Thus, by comparing the value in EIP against our pattern offset, we determine that 267 bytes are needed to overwrite the return address and redirect execution to our NOP sled. Let's proceed to the next step

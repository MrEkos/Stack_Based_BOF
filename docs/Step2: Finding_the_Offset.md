## Creatting the pattern

To ensure precision in the process, a unique pattern was generated and provided as input using tools such as pattern_create from the Metasploit framework or cyclic from pwntools:

<img width="1277" height="761" alt="1" src="https://github.com/user-attachments/assets/10b7f075-e6a3-41af-a7e5-cbb89c524022" />


(With GEF / Without GEF)  

The precise offset at which the EIP register is rewritten can be found using this pattern. Comparing EIP value against this pattern.

When we overwrite EIP for the first time, the number of bytes we utilize defines the -l flag, which indicates the pattern's length.

---

## Sending the pattern

We must now supply the program with the pattern as input. 

<img width="1279" height="212" alt="2" src="https://github.com/user-attachments/assets/8604b7dd-f0b9-4dd0-869b-c3bdab167a41" />




(With GEF / Without GEF)

```bash
r $(python3 -c "import sys; sys.stdout.buffer.write(b'pattern')")  # Syntax
```

This time, when the program crashed we see a different value on the EIP register. 

<img width="1279" height="767" alt="3" src="https://github.com/user-attachments/assets/0e14edf1-3d3d-40ae-b34c-22a3a75460a8" />



(With GEF / Without GEF)

---

## Locating the offset

Now, using the pattern_offset tool from metasploit framwework we are going to locate the exact amount of bytes needed to reach EIP with the command displayed on the image below (with gef and without )

<img width="1268" height="110" alt="4" src="https://github.com/user-attachments/assets/572dd4cd-b7fc-4aca-b2af-693750b2ab70" />


(With GEF / Without GEF)

Thus, by comparing the value in EIP against our pattern offset, we determine that 267 bytes are needed to overwrite the return address and redirect execution to our NOP sled. Let's proceed to the next step

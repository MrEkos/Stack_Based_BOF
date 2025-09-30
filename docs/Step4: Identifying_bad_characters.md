# Bad Characters

## Testing Process
- Inject the previous payload followed by a sequential list of bytes (\x00 to \xff) into the buffer.
- Inspect memory in gdb to confirm which bytes were preserved.
- Exclude problematic bytes (commonly \x00, \x0a, \x0d, etc.).

## Why It Matters
- Bad characters can truncate the payload or corrupt the shellcode.
- Identifying and removing them ensures reliable exploit execution.

## Characters list
"\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"

- The total size of this list is 256 bytes
- We must discard \x00 (NULL) since it is a bad character in most contexts.
- After removing \x00, the list length is 255 bytes.
(\x0a (LF) and \x0d (CR) are often bad as well, depending on how input is parsed. Always verify in the target.)

---

## Step by step requirements and example:
- Repeat this process until all bad characters are removed.
- You must have already identified the offset to EIP (see Stack-Based_Buffer_Overflows.md and Step2: Finding_the_Offset.md)

---  

## Identifying the Vulnerable Function and Testing for Bad Characters:

### Initial Analysis Setup:
- Use the command 'set disassembly-flavor intel' to make everything more human readable

### Function Identification:
- Use "info functions" to list the program symbols.

<img width="557" height="649" alt="1" src="https://github.com/user-attachments/assets/067952fd-f369-412d-a44f-8d89df88bcf1" />


- Disassemble suspicious functions (particularly those handling user input): 'disas function_name'

<img width="795" height="761" alt="2" src="https://github.com/user-attachments/assets/d02d7fa1-e029-4381-b14a-bd3717c1ddab" />



<img width="786" height="476" alt="3" src="https://github.com/user-attachments/assets/67716b1f-ea17-441c-b721-e71c0e4b955f" />


### Bad Character Analysis Methodology
- Set a breakpoint at the vulnerable function to analyze memory state: 'break function_name'

### Crafting the Payload Structure
- The payload structure for bad character testing should be: ```[Padding to reach EIP] + [Bad Character Test Sequence] + [EIP Overwrite Marker]```
- Execute the program with the test pattern:

```bash
run $(python3 -c "import sys; sys.stdout.buffer.write(b'E' * (267 - 255) + b'\x01\x02...SNIP...\xfe\xfe' + b'k' * 4") # Option 1 (more manual)

run $(python3 -c "import sys; offset = 267; bad = {}; char = bytes(c for c in range(1,256) if c not in bad); eip = b'k' * 4; payload = b'E' * (offset - len(char)) + char + eip; sys.stdout.buffer.write(payload) # Option 2 (Saving values to variables, more automated)

```
### Tips:
- My favorite option is #2: You can add your bad chars to the dictionary, for example: bad = {0x09, 0x0a}, and the chars = bytes(c for c in range(1, 256) if c not in bad) list automatically excludes them, and the padding auto recalculates with b'E' * (offset - len(chars)).
  
- Option #1 is manual: every time you find a bad byte you must remove it by hand from the literal sequence and manually adjust the padding.
  
### Memory Examination Technique
- After the crash/breakpoint, examine the stack memory: ```x/400xb $esp```
- Adjust the examination range based on your buffer size and stack layout.

### Bad Character Identification Process

A- Locate Your Test Sequence: Find where your character sequence lands in memory.

<img width="771" height="627" alt="4" src="https://github.com/user-attachments/assets/05212181-59fe-47e6-944d-2e14385f2a78" />



B.- Compare byte by byte: Verify each character from \x01 to \xff appears in order.

C.- Identify anomalies, look for:

 - Missing characters (gaps in sequence)
 - Modified bytes (unexpected values)
 - Truncated sequences (early termination)

<img width="779" height="631" alt="5" src="https://github.com/user-attachments/assets/48adc899-4c47-4ed5-a8da-fe03e3e70bee" />


(Expected = 0x09 - Found = 0x00. 0x09 must be included to bad.)


### Iterative Testing Approach: For each identified bad character

- Remove it from your test sequence
- Regenerate the payload
- Repeat the examination process
- Continue until no more bad characters are detected

After successfully identifying and removing all bad characters through iterative testing, we now could have a clean payload that will execute reliably in the target environment. The next critical step is to locate a valid return address for redirecting program execution to our shellcode. See you there.

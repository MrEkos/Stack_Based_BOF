# Disclaimer
Since this is a critical and dangerous step, remember this is for educational purposes only. Never use this knowledge to harm anyone. I am not responsible for any misuse of this information. For obvious reasons, I will explain only the basic steps to generate shellcode using simple tools like Shellcraft and MSFvenom. I will use the lab context in the explanation. If you want to learn more about shellcode, feel free to research it online.

# Generating the Shellcode:

Now that we control the execution flow of our vulnerable program, let’s craft our shellcode. This is a vital step in buffer overflow exploitation. Every environment is different and many details can change, so it is important to adapt the executable code to each scenario. We have prepared for this moment by identifying bad characters and calculating the NOP sled length and the approximate shellcode length.

We know that we have 267 bytes available between our input buffer and EIP. We will allocate 100 bytes for the NOP sled and reserve about 150 bytes for the payload.

We need executable code that, when reached by the CPU, prints the contents of /root/archive.txt. For this purpose, we will use a high level Pwntools Shellcraft template to write the file’s contents to stdout, this is not the only way to create shellcode. To adapt to diverse environments, you should master multiple approaches like hand writing it in assembly, composing and chaining syscalls, re encoding or transforming payloads, and stitching staged or modular shellcode. Feel free to explore these methods on your own.

- Looking the template:

<img width="470" height="179" alt="1" src="https://github.com/user-attachments/assets/d525969f-7a4d-4a8a-b189-a6a026c8dd6c" />

- Crafting the payload:

<img width="1035" height="71" alt="2" src="https://github.com/user-attachments/assets/1486afb7-e938-43bd-85a1-358ae8d2e58a" />


Note: file descriptor 1 is stdout.

Now we must verify that our shellcode does not contain any bad characters. We will use msfvenom for this.

- Saving our shellcode bytes on a file

<img width="1281" height="73" alt="3" src="https://github.com/user-attachments/assets/88d32570-07a8-45bf-9371-5e9270e44eab" />


- Cleaning and weighing our shellcode with msfvenom:

<img width="1032" height="165" alt="4" src="https://github.com/user-attachments/assets/2553727b-fe36-410d-9f76-ecdf7e777d6c" />


Our final shellcode, after removing bad characters, is 50 bytes in total. We can repeat now the ***Saving our shellcode bytes on a file*** above in order to re save the bytes of our clean payload inside an archive and use the Exploit_Builder.py on the exploit section to complete our payload  


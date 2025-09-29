## GEF (GDB Enhanced Features)
This guide uses GEF, a professional GDB extension that provides:
- Enhanced register and memory visualization
- Integrated pattern creation and offset calculation
- Real time context awareness during debugging
- Installation:
  
```bash
wget -q -O ~/.gdbinit-gef.py https://github.com/hugsy/gef/raw/master/gef.py
echo "source ~/.gdbinit-gef.py" >> ~/.gdbinit
```


## Having problems with the requirements.txt ?

<img width="1278" height="858" alt="image" src="https://github.com/user-attachments/assets/7e788549-cd71-4722-bf21-9e5aed70ebca" />


You’re seeing that error because Kali (Debian-based) protects the system Python from being modified by pip.

## Solving the problem

A Python virtual environment is an isolated copy of a Python interpreter and its packages for a specific project, preventing dependency conflicts between different projects and with the global Python installation. Tools like venv (included in Python 3.3+) allow you to create these environments, which are then activated so you can use their own versions of libraries and modules with pip.

<img width="1278" height="859" alt="image" src="https://github.com/user-attachments/assets/51b7e7ce-2592-457f-8221-01679670d74f" />

```bash
python3 -m venv 'Your_Virtual_Enviroment_Name'  # Creating the virtual enviroment with the integrated module venv
ls -la | grep -i Your  # Confirming the creation
source ~/'Your_Virtual_Enviroment_Name'/bin/activate  # Activating the virtual enviroment
python3 -m install -r requirements.txt  # Starting the download process

```

<img width="1281" height="858" alt="image" src="https://github.com/user-attachments/assets/1b9f4ab6-b966-4837-a625-6d0b772a1bca" />

```bash
pytyhon3 -m pip freeze | grep pwn  # Listing the installed packages and looking for pwntools
```

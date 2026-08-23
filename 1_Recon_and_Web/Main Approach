A professional approach to reconnaissance is built on a strict methodology: **from broad network coverage to fine-grained service enumeration**. Time can't be wasted on blind enumeration until a complete picture of open ports and services is established.

## 1. Network Discovery & Port Scanning

The first step is a two-phase scan: instant discovery of all open ports, then deep version collection based only on the discovered ports.

### Nmap (Network Mapper)

```
# Phase 1: Quick scan of all 65,535 ports, filtering out closed ports and without DNS resolution
sudo nmap -p- --min-rate 5000 -Pn -n -T4 --open 10.10.11.XX -oN all_ports.txt

# Phase 2: Targeted collection of versions and default NSE scripts for found ports (example: 22,80,445)
sudo nmap -sC -sV -p 22,80,445 -Pn 10.10.11.XX -oA target_targeted

# Background scan of key UDP ports (SNMP, DNS, TFTP):
sudo nmap -sU --top-ports 20 -Pn 10.10.11.XX -oN udp_top20.txt
```

## 2. Web Reconnaissance

If ports 80, 443, 8080, 8443, 5000, 3000, etc. are open.

### Virtual Hosts and Subdomains (VHost / Subdomain Fuzzing)

Critically important for CTFs and real-world tests when the default page is served:

```
# Search for subdomains / VHosts via ffuf
ffuf -u http://target.htb -H "Host: FUZZ.target.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs <default_response_size>
```

### Directories and Files (Content Discovery)

```
# Feroxbuster (fast recursive search in Rust)
feroxbuster -u http://10.10.11.XX -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,txt,json,bak -t 50

# Gobuster (classic alternative fuzzer)
gobuster dir -u http://10.10.11.XX -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,txt,zip,bak -t 30
```

### Specific Web Recon

- **WhatWeb / Wappalyzer:** Detect engine, web server, and libraries:

```
whatweb http://10.10.11.XX
```

- **WPScan (WordPress):**

```
wpscan --url http://target.htb --enumerate u,vp,vt --api-token YOUR_TOKEN
```
## 3. Windows / Active Directory / SMB Reconnaissance

If ports 139, 445, 88 (Kerberos), 389 (LDAP), and 5985 (WinRM) are open.

### NetExec / CrackMapExec (Swiss Army Knife for Infrastructure)

```
# Checking anonymous / null sessions in SMB
nxc smb 10.10.11.XX -u '' -p '' --shares
nxc smb 10.10.11.XX -u 'guest' -p '' --shares

# Checking WinRM / RDP for credentials
nxc winrm 10.10.11.XX -u user.txt -p pass.txt
```

### SMB & RPC Enumeration

```
# Checking shared resources via smbclient
smbclient -L //10.10.11.XX -N

# Interactive connection without a password
smbclient //10.10.11.XX/SharedFolder -N

# Full user/group information dump via RPC
rpcclient -U "" -N 10.10.11.XX
# Inside rpcclient: enumdomusers, queryusergroups, enumdomgroups

# Automated enum4linux-ng
enum4linux-ng -A 10.10.11.XX
```

### LDAP / Kerberos (Active Directory)

```
# Anonymous LDAP collection
ldapsearch -x -H ldap://10.10.11.XX -b "DC=domain,DC=local"

# User validation via Kerberos without credentials (Kerbrute)
kerbrute userenum --dc 10.10.11.XX -d domain.local /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

## 4. Specific Protocols & Databases Recon

- **SNMP (Simple Network Management Protocol, UDP 161):**

```
# Brute-force community strings (public, private) and dump processes/users
onesixtyone 10.10.11.XX
snmpwalk -v2c -c public 10.10.11.XX
```

- **NFS (Network File System, Port 2049):**

```
showmount -e 10.10.11.XX
sudo mount -t nfs 10.10.11.XX:/share /mnt/target
```

- **Databases (MySQL) 3306, MSSQL 1433, Redis 6379):**

```
# MSSQL (Impacket)
impacket-mssqlclient -port 1433 target.local/guest@10.10.11.XX -windows-auth

# Redis (check for unauthorized access)
redis-cli -h 10.10.11.XX ping
redis-cli -h 10.10.11.XX info
```

## 5. Methodological Cheat Sheet for Time Management

|**Stage**|**Tool**|**Time Limit**|**What to Look For**|
|---|---|---|---|
|**All-Ports Scan**|`nmap` / `rustscan`|2–3 min|Non-standard ports (8000+, 3000+, high-ports)|
|**Version Fingerprint**|`nmap -sC -sV`|3–5 min|Exact software versions (Google / Searchsploit software)|
|**Content Discovery**|`ffuf` / `feroxbuster`|5–10 min|Admin panels, `.git`, `api/`, configs, backups|
|**Auth / Shares**|`nxc` / `smbclient`|5 min|Null sessions, read/write folders|

> **Main rule:** If you receive a specific banner/version $\rightarrow$, immediately look for known vectors and `Exploit-DB` / `GitHub` for this version, don't try to brute-force logins and directories blindly for hours.

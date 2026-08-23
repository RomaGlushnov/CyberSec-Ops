### 1. Gobuster
1. Search for hidden directories and files (with extensions and without SSL)
```
gobuster dir -u https://10.10.10.10/ -w /usr/share/wordlists/dirb/common.txt -x php,txt,html,bak -k -t 50
```

2. Search for subdomains (DNS fuzzing)
```
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 30
```

2. Search for virtual hosts (VHost fuzzing) — critical for CTF!
```
gobuster vhost -u http://target.htb/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -t 50
```

### 2. FFUF (Fuzz Faster U Fool)

The most flexible tool. Use the word `FUZZ` where you want to iterate over values ​​(URLs, headers, parameters).

Search for directories and files (with extensions):
```
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -e .php,.txt,.bak -c
```

Virtual host fuzzing (VHost). Finding subdomains. If the server responds with a 200 response code for everything, use -fs to filter out the default page size:
```
ffuf -u http://target.htb -H "Host: FUZZ.target.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -mc 200 -c -fs 1250
```

Fuzzing GET parameters (searching for hidden parameters, such as ?admin=1):
```
ffuf -u http://10.10.10.10/index.php?FUZZ=test -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 0
```

Fuzzing POST data (with proxying) in Burp Suite for analysis):
```
ffuf -X POST -d "username=admin&password=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.10.10/login -w /usr/share/wordlists/rockyou.txt -x http://127.0.0.1:8080
```

> [!tip] Response filtering (the most important thing in ffuf): `-mc 200,301` — Match Code (show ONLY these codes). `-fc 404,403` — Filter Code (HIDE these codes). `-fs 42` — Filter Size (hide responses larger than 42 bytes). `-fw 12` — Filter Words (hide responses with exactly 12 words).

### 3. Feroxbuster

Ideal for initial reconnaissance. Its main feature is automatic recursion (it finds a folder and automatically fuzzes inside it).

Basic recursive launch:
```
feroxbuster -u http://10.10.10.10/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

Search for files with extensions, ignore 404/403 codes, and limit recursion depth (to avoid freezing):
```
feroxbuster -u http://10.10.10.10/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,txt,zip,bak -C 404,403 -d 2
```

Proxy all traffic to Burp and disable SSL verification:
```
feroxbuster -u https://10.10.10.10/ -w /usr/share/wordlists/dirb/common.txt --insecure --proxy http://127.0.0.1:8080
```

### 4. Wfuzz

Great for exploiting LFI/SQLi vulnerabilities via fuzzing, when you need to filter results by the number of lines or words.

LFI (Local File Inclusion) search. Ignore responses with 0 words (`--hw 0`):
```
wfuzz -c -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt --hw 0 http://10.10.10.10/index.php?page=FUZZ
```

Fuzzing API endpoints with a JSON header. Hide the 404 error code (`--hc 404`):
```
wfuzz -c -w /usr/share/wordlists/dirb/common.txt -H "Accept: application/json" --hc 404
```

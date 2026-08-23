### 1. IP Attack Search and Brute Force Analysis (Question 1)

Identifying IP addresses with the most failed login attempts:

```
grep "Failed password" auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr
```

Finding brute force attempts with non-existent usernames:

```
grep "Invalid user" auth.log
```
---
### 2. Identifying a compromised account (Question 2)

Finding successful SSH (Secure Shell) logins:

```
grep "Accepted password" auth.log
```

Viewing successful login attempts from a specific IP address:

```
grep "Accepted" auth.log | grep "65.2.161.68"
```
---
### 3. Analyzing sessions and reading the `wtmp` binary (Question 3)

Parsing the `wtmp` binary using the `utmp.py` script from the `last` writeup (if the `last` utility fails due to architecture):

```
python3 utmp.py -o wtmp.out wtmp && cat wtmp.out
```

Quickly viewing text lines from a binary file without parsers:

```
strings wtmp | grep -C 3 "65.2.161.68"
```

Standard session view with full time and IP using `last`:

```
last -f wtmp -F -i
```
---
### 4. Finding the Session ID (Question 4)

Finding the creation of a new session by the `systemd-logind` init service:

```
grep "systemd-logind" auth.log | grep "root"
```

Finding the session opening string by the PAM (Pluggable Authentication Modules) module:

```
grep "session opened" auth.log | grep "65.2.161.68"
```
---

### 5. Search for created backdoor accounts (Question 5)

Search for new users added to the system:

```
grep -E "useradd|adduser" auth.log
```

Search for privilege changes and additions to the administrator group:

```
grep -E "usermod|groupadd" auth.log
```
---

### 6. Session end time (Question 7)

Search for the session closing time by session number or user:

```
grep "session closed" auth.log | grep "root"
```

---

### 7. Analyzing commands executed via sudo (Question 8)

Output of all commands run with elevated privileges:

```
grep "COMMAND=" auth.log
```

Filtering commands for a specific user:

```
grep "sudo:" auth.log | grep "cyberjunkie"
```

```
ports=$(nmap -p- --min-rate=1000 -Pn -T4 10.129.81.77 | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed 's/,$//')
nmap -p$ports -Pn -sC -sV 10.129.81.77
```
- **`-p-`** — check all 65,535 ports (**TCP** — **Transmission Control Protocol**).
- **`--min-rate=1000`** — send at least 1000 packets per second to speed up the process.
- **`-Pn`** — do not perform a preliminary verification request (**ping**), consider the host active.
- **`-T4`** — aggressive timing for faster scanning.
- **`grep '^[0-9]'`**: filters the output, leaving only lines starting with a digit (ignores **Nmap** header and footer).
- **`cut -d '/' -f 1`**: splits the line on the `/` character and takes the first part (i.e. the port number itself, cutting off the protocol and state).
- **`tr '\n' ','`**: replaces all newlines with commas.
- **`sed s/,$//`**: removes the last extra comma at the end of the line

nmap -p- -sV -sC -Pn --min-rate 1000 -T4 <IP>

sudo echo "10.129.244.146 http://orion.htb/" | sudo tee -a /etc/hosts > /dev/null
append to the end of the file

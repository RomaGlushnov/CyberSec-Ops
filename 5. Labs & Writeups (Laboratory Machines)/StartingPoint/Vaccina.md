![Pasted image 20260910191826](img/Pasted%20image%2020260910191826.png)![Pasted image 20260910191845](img/Pasted%20image%2020260910191845.png)![Pasted image 20260910191901](img/Pasted%20image%2020260910191901.png)
![Pasted image 20260910192358](img/Pasted%20image%2020260910192358.png)
![Pasted image 20260910192115](img/Pasted%20image%2020260910192115.png)
2cb42f8734ea607eefed3b70af13bbd3  это хеш пароля, зашифрованный алгоритмом **MD5**
![Pasted image 20260910192620](img/Pasted%20image%2020260910192620.png)
Using this to login on http server . 
![Pasted image 20260910192843](img/Pasted%20image%2020260910192843.png)

![Pasted image 20260910192943](img/Pasted%20image%2020260910192943.png)
Seccesfull

![Pasted image 20260910193829](img/Pasted%20image%2020260910193829.png)
Vulnerable to sql injection

Firstly we will copy cookie 
F12>Storage>Cookie 
![Pasted image 20260910194246](img/Pasted%20image%2020260910194246.png)

We will use`sqlmap` (a tool for the automated discovery and exploitation of SQL injections.) with this cookie.

![Pasted image 20260910200347](img/Pasted%20image%2020260910200347.png)
![Pasted image 20260910200435](img/Pasted%20image%2020260910200435.png)
![Pasted image 20260910200601](img/Pasted%20image%2020260910200601.png)
We created shell that will run all commands on server. 
![Pasted image 20260910200254](img/Pasted%20image%2020260910200254.png)
deffolt system user with restricted rights
![Pasted image 20260910201038](img/Pasted%20image%2020260910201038.png)

![Pasted image 20260910201215](img/Pasted%20image%2020260910201215.png)
![Pasted image 20260910202215](img/Pasted%20image%2020260910202215.png)
Something interesting : uid=1000 default user but this real user in sudo group and  can execute absolutely any command with root privileges.

Let's view the list of all databases on the server.
![Pasted image 20260910203553](img/Pasted%20image%2020260910203553.png)

Let's connect to a specific database.
![Pasted image 20260910204401](img/Pasted%20image%2020260910204401.png)

![Pasted image 20260910213114](img/Pasted%20image%2020260910213114.png)
Such us we can't read that file we encode  that file in base64 then decode on our machine
![Pasted image 20260910212936](img/Pasted%20image%2020260910212936.png)

![Pasted image 20260910212844](img/Pasted%20image%2020260910212844.png)
Found login and pass
![Pasted image 20260910212751](img/Pasted%20image%2020260910212751.png)

We will use this pass and login to connect ssh 
![Pasted image 20260910213354](img/Pasted%20image%2020260910213354.png)

We took first flag  
ec9b13ca4d6229cd5cc1e09980965bf7
![Pasted image 20260910213442](img/Pasted%20image%2020260910213442.png)


Let's look at the utilities in the sudo list.
![Pasted image 20260910221116](img/Pasted%20image%2020260910221116.png)

![Pasted image 20260910221008](img/Pasted%20image%2020260910221008.png)
Enter
How we see we took root 
![Pasted image 20260910221758](img/Pasted%20image%2020260910221758.png)

Lets take last flag 
![Pasted image 20260910222042](img/Pasted%20image%2020260910222042.png)
dd6e058e814260bc70e9bbdef2715849

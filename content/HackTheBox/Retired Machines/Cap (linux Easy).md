
## Enumeration
- Run an nmap scan
- found ftp, ssh and http ports open on the machine
- first I will try to access ftp and see if there is anything useful there
-- so turns out that i cant log into anonymous on ftp
- I tried sqlmap to test for sql injections, doesnt seem like it
- now using gobuster to look for directories i can access
- I got stuck

## answer
- test around the website and change the id on /data/[id]
- get the past pcap files and look through for credentials
- found credentials
- use it to log through ftp and ssh
- user.txt
- now its time for privilege escalation
- we download linpeas.sh script that looks for ways to escalate privileges in linux
- run a python server
- curl the file and pipe it into bash
- we found 'Files with capabilities (limited to 50):
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip'
- they gave this file permissions so that they can run administrative functions like netstat on the http server
- now we just need to run that version of python and give it commands to give us root
- `import os; os.setuid(0); os.system("/bin/bash")`
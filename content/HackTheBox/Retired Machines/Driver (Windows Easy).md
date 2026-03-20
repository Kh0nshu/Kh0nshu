## Enumeration
- As usual we will be running an nmap scan to start first
- It has a bunch of open ports, port 80, 445, and 5985 stand out
	- 80 and 5985 are running http
	- 445 is smb
	- 135 is running msrpc, which you could send it a function to run and tell you the result
- First lets check the http ports
	- it prompts asking for a password and username, "admin admin" works.
	- It's a firmware update center
	- You can upload a file and the website runs on php
		- possible reverse shell?
	- has the domain driver.htb so lets add that to /etc/hosts
- The nmap script says that it was able to connect to smb as a guest account so let's see what we can find
	- for some reason i can't log in as guest
- There has to be something that we can do with the firmware updates page to upload some sort of malicious file.
	- uploading php reverse shell doesn't work because its not executing it
	- apparently the files you upload get sent to their smb

## Learned
- smb share scf file attack
	- you upload an scf file with specific commands
	- run `responder -I tun0`
	- get the NTLM hash of the user
- When you have credentials use evil-winrm to connect to windows remote management (WinRM)
	- `evil-winrm -i {ip} -u {user} -p 'password'`
- Apparently there is a file that shows the powershell history of the user
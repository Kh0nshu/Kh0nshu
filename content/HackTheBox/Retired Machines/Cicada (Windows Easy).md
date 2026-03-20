## Enumeration
- As usual run a full nmap scan of the machine
	- we find an overwhelming 13 ports open
	- This is the first time I've encountered so many open ports on a machine
	- The interesting ones seem to be 3 Active Directory ports open, there is an http port open too
- So apparently smb has anonymous login enabled and there is a fileshare "HR" that can be accessed
	- It has a default password notice to change
	- so now our goal is to find the user who hasn't yet changed his default password.
	- The question is how do we find the username? 
		- Which service can we look through to find usernames on a server
		- Apparently there is this thing called impacket and we can use it's lookupsid to find possible usernames

## New Tool Learned
- **crackmapexec** : a tool to automate enumerating domains (including users, files/directories, and shares). **This tool is GOATED for Windows Machines!!**
	- Can also brute force with supplied credentials or dump usernames and hashed passwords in active directory environments. 
- **Impacket's lookupsid** : a tool that will try brute forcing windows security identifiers (SID) of any users in the AD domain.
	- `impacket-lookupsid '10.10.11.35/guest'@10.10.11.35 -no-pass`
	- That shows all the users on the AD

## Now back to the problem
- Now that we have a list of users, we want to use crackmapexec to brute force test them with the default password we found. (Also known as **Password Spraying Attack**)
- We find the user 'michael.wrightson' still using the default password.
- Problem is that this user doesn't have any permission to open of the smb file shares.
	- So we run `crackmapexec smb 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --users` to find other users
	- we happen to find a user  that put his password in his description in case he forgets it.
		- User: david.orelious Password:aRt$Lp#7t\*VQ!3
		- now we run `crackmapexec smb 10.10.11.35 -u david.orelious -p 'aRt$Lp#7t*VQ!3' --shares` to see what shares this user has access to
	- He has access to the DEV share as READ
	- Which has a backup script with a username and password saved in plaintext.
	- User: emily.oscars Password:Q!3@Lp#M6b*7t*Vt
	- This user has permission to read and write to default share and read the remote admin share.
- Now we need to check if we can connect using emily.oscars through evil-winrm to get a remote shell.
	- Which yes it works (Here we navigate to desktop and **get the user flag**)

## Privilege Escalation 
- So now we check what privileges we have using `whoami /priv`
- So we see that this user has `SeBackupPrivilege`, which is typically given to either service or admin accounts. Meaning that it allows us access to system protected files.
- The files we are most interested in are System and Sam windows registry hives. Which should have what we need to escalate our privileges.
	- SAM(Security Account Manager) hive: has local user account and group info, including their hashed passwords.
	- System hive:  has system config settings, like the system boot key which is used to decrypt the password hashes in SAM
- use `reg save hklm\[sam/system] [sam/system]` to copy those 2 registries to 2 files in the current directory.
- Now we use 'Impacket's secretsdump' to make it dump the user NTLM hashes, which we can use the pass-the-hash attack
	- `impacket-secretsdump -sam sam -system system local
	- Now we have the Administrator hash
- Log in through evil-winrm `evil-winrm -u Administrator -H 2b87e7c93a3e8a0ea4a581937016f341 -i 10.10.11.35`



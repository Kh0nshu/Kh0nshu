## Enumeration
- So we run an nmap scan and find out that ssh, http, and https are open
- we open the site on firefox and its just an http server test page
- I tried looking around for anything hidden or looking for vulnerabilities in the appache version
- Found the domain in an http request
- Then got the real website and found the version of word press
- used a cve that shows private unpublished drafts
- which got me access to the company chat
- There is a bot that I used to get it's own configuration file and it gave me the password to a user on the machine that i logged into 

## Privilege Escalation
- So apparently there is a program called polkit that acts as the middle man to check if you have permission
	- If you have permission it lets you do what you want, if not should we ask root, or just  say no.
- 
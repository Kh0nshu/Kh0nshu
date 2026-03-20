## Enumeration
- So we run an nmap scan as usual
- we have 2 open ports (22, ssh and 55555 unknown), and we have 2 filtered ports (80 http, and 8338 unknown)
- lets try accessing port 80 from the browser
	- Didnt work
- Now lets try accessing port 55555 to know whats on there
	- We find a basket website powered by request-baskets
- We have  [request-baskets](https://github.com/darklynx/request-baskets) | Version: 1.2.1 so we can look for vulnerabilities in that software
- So we find the fact that we can reroute when someone clicks on the link somewhere else, and we can use that to trick the machine into opening the filtered port 80
## Exploitation
- now we opened port 80, and we find it running some software
- we look up vulnerablilities for that software and find an exploit ready to use
- we use it and it give us a reverse shell into the machine

## Escalation 
- so we run sudo -l to check our privalages and we find out we can run systemctl in sudo without password
- and we look up systemctl vulnerabilities
- turns out that when you run it, if the output is too much it will run it through less  with root permission
- so we can run commands through less to get bin/bash as root
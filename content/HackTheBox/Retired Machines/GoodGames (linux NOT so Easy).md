## Enumeration 
- so I've been messing around this website for a while and trying sql injection techniques i know and nothing has been working.
- When i got frustrated i checked the machine info and it says that it has an sql injection vulnerability so I'm going to have to go through the walkthrough to understand
- I tried looking up Union-Based SQL injections but still

## Answer
- I guess i was overthinking it, you just had to do `admin' or 1=1 #`
- then save the request from burpsuite in a txt file
- And run it through sqlmap `sqlmap -r request.txt --dbs` this will make it list databases it finds
- So we found 2 (main and information_schema)
- when we look further using `sqlmap -r request.txt --tables -D main` we find a users table in the main database
- Then we log into admin account and find a way to get into internal administration subdomain when we click on settings
- There is a way for you to change your name so we input {{7\*7}} and it returns 49 as our name so its vulnerable to SSTI
- When we find that out we can create a reverse shell payload and input it encoded in base64 ( you'll have to figure out how the backend is configured in this case python 3.8.7 and then find a payload that works on python and make it run your command piped into base64 to decode it)
#### Privilege Escalation
- we run ls -la and we get UID 1000 instead of the name of the user Augustus, so we know we are in a docker, a sandbox for the application
- checking mount shows us that its mounted with rw permissions
- we check our ip and its 172.19.0.2, because docker usually assigns the first address to the host
- we can use bash instead of nmap to check open ports `for PORT in {0..1000}; do timeout 1 bash -c "</dev/tcp/172.19.0.1/$PORT &>/dev/null" 2>/dev/null && echo "port $PORT is open"; done`
- we find ssh is open, we log in with the same password we  found earlier for the admin to the agustus user and it works
- the user directory is mounted on the docker, so we can make a file in host and change the persmission in the container
- we copy bash  and we go back to the root in the docker and run `chown root:root bash` and `chmod 4755 bash`
-  now we go back through ssh and we run ./bash -p and we have root
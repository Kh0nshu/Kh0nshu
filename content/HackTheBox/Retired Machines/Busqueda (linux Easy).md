---
title: Busqueda (Linux Easy)
draft: false
type: HackTheBox
summary: Busqueda is an Easy Difficulty Linux machine that involves exploiting a command injection vulnerability present in a Python module.
date: 2026-04-22T10:00:00-05:00
---
- nmap scan found ssh and http ports open'
- we found a domain name to add to /etc/hosts searcher.htb
- we are directed to a webpage with just one text input
- I'm gonna run gobuster on this site for now while we look around for interesting things to do.
- Tried to see if there is any type of sql injection i can do for the query or search engine inputs
- Then used Wappalyzer to look at the tech stack and frame work of the site.
	- Looking for any vulnerabilities in the version of python or flask
- Looked down in the site, it says Powered by [Flask](https://flask.palletsprojects.com) and [Searchor 2.4.0](https://github.com/ArjunSharda/Searchor)
	- A quick search for vulnerbilities in searcher 2.4.0
	- Found an arbitrary code execution vulnerability, now i have a remote shell
	- user flag `ed99432243fdc24e0f22d623ddaad8d3`

## Privilege Escalation
- Found credentials in `/var/www/app/.git/config` (plaintext password in the git remote URL)
- Used that password on `sudo -l` (password reuse) to see what svc could run as root
- Found svc could run `system-checkup.py` as root
- Used `docker-inspect` to dump environment variables from the MySQL container, finding more credentials
- Logged into Gitea as administrator to read the source code of `system-checkup.py`
- Discovered `full-checkup` calls `./full-checkup.sh` using a **relative path**
- Created our own malicious `full-checkup.sh` in `/tmp` that sets the SUID bit on `/bin/bash`
- Ran the sudo command from `/tmp`, which executed our script as root instead of the real one
- Ran `bash -p` to get a root shell
- root flag `c56628f4bb6e222d277f827fb09a6da9
`
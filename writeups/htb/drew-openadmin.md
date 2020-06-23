## Drew's HackTheBox OpenAdmin writeup
#### original article can be found at https://medium.com/@agipson/hackthebox-openadmin-write-up-aeeb1a156f9d
OpenAdmin is the first ‘real’ box I’ve rooted on HackTheBox and it was an enlightening experience. The main foothold here is a remote code execution vulnerability that exists in OpenNetworkAdmin v18.1.1.
To enumerate exposed services, perform an nmap scan:  
`nmap -sV -T4 10.10.10.171`
In the resulting output we notice that:  
1. The box is running an Apache server (v2.4.29) on port 80  
2. The box has SSH open on port 22.  

At this point, there are two paths that seem viable: drill down into the HTTP server, or look into Apache 2.4.29 exploits. During my run, I spent a little more time than I would like to admit trying to understand CVE-2019–0211, which affects Apache 2.4.29. Once I realized it was a local exploit, I moved on — next up, see what else can be found through HTTP.  
  
To enumerate sub-directories that might exist on the HTTP server, use something like *gobuster*:  
`gobuster dir -u <http://10.10.10.171/> -w /usr/share/wordlists/SecLists/directory-list-lowercase-2.3-small.txt >> gob.out`  
  
We are able to find a 301-redirect at http://10.10.10.171/music — navigating here in a browser brings us to a splash page, with an obvious link to a login page.  
The login link brings us to http://10.10.10.171/ona/, where we are presented with guest access to an OpenNetworkAdmin (ONA) portal. 
  
![OpenNetworkAdmin portal](https://miro.medium.com/max/1400/0*U2YZ-h9qrMB82H4m)  

The polar chart that Hack The Box provides for this lab shows it is CVE-heavy. Since we are presented with the Newer Version Available notice and the version number for ONA, we can guess that there’s some available exploit for OpenNetAdmin v18.1.1.  
Let’s find out with a quick searchsploit:  
`searchsploit opennetadmin 18.1.1`  
  
![searchsploit opennetadmin](https://miro.medium.com/max/1400/0*Zjb6B8ETx6nH-Pp-)  
  
Let’s avoid Metasploit, and go with the bash script (47691.sh). Usage of the vanilla script goes something like this:   
  
![bash script](https://miro.medium.com/max/1234/0*j5n95lms-58h8fdE)  
  
Something’s not right, and it has to do with End-Of-Line character differences between Linux and Windows — there is a good explanation of this concept here. TL;DR: Use dos2unix. Perl saves the day!  
  
With our script modified, we can try again — this time, access works and we have a local shell. At this point, we can poke around a bit.  
`ls -al; whoami; id; sudo -l; env`  
![Initial enum](https://miro.medium.com/max/1400/0*gsSbCXJQ2pHXP0yI)  
Basic local enumeration  
  
![etc passwd](https://miro.medium.com/max/1400/0*ItDyVLsjdtE2maG5)  
cat /etc/passwd  
  
Oh yeah!
  
There are lots of interesting files to look through here. From `/etc/passwd` we can enumerate two users, `jimmy` and `joanna`. Beyond this, we are mostly limited in scope to `/opt/ona`. There is something REALLY interesting to be found in `/local/config/database_settings.inc.php`:  
![config file](https://miro.medium.com/max/1212/0*ytLWmr4cb87nNs2Q)  
  
Someone left a MySQL password here. Since we are going for user-level access and we now know 2 users, let’s try jumping straight through SSH. That someone might’ve gotten lazy with their passwords…  
![ssh joanna permission denied](https://miro.medium.com/max/1224/0*OD9hMw2hADcMn-fs)  
No luck with `joanna`.  

![ssh jimmy](https://miro.medium.com/max/1400/0*fPlXJtPXg8XxxH9s)  
Different story with `jimmy` - we're in!
  
We can’t do much as `jimmy` - makes sense. Someone get this guy on a password manager.
  
But even `jimmy` has to debug logs sometimes, so it's worth looking to see if we have access to anything in `/var`: `find /var -type f -user jimmy 2>/dev/null`. If we recall that jimmy's UID/GUID are `1000`, we can refine further:
`find /var -type f -user jimmy 2>/dev/null | grep -v 1000`
![find grep](https://miro.medium.com/max/1400/0*YznwDJNu_MrWX8rI)  

Apparently Apache2 servers store their files in `/var/www/html` by default! Who would've thought to RTFM. And even better, there is an `/internal` folder, containing `main.php`:  
![cat main.php](https://miro.medium.com/max/1400/0*AZeZVAjiDaTVj_R1)  
  
Since we know now that we are in the web-root folder, maybe this PHP file can be called across HTTP. It appears that it will give us joanna's private RSA key.  
`curl http://127.0.0.1/main.php`  
  
![curl main](https://miro.medium.com/max/1400/0*yAfS0LeU-FVsxSEu)
  
However, a quick attempt to call the script fails. Thankfully, the 404 output reminds us we are checking only at port 80 — it is quite possible that this Apache installation has two IP-based virtual hosts (explained here), and the other is hosting /internal. Let's use netstat to check listening TCP ports:  

  
![netstat](https://miro.medium.com/max/1400/0*cQ2pI1hoRItNXQep)
  
Most of these can be accounted for: 3306 for MySQL, 53 for DNS, & 22 for SSH — however, port 52846 stands out. So, instead of the implict :80, let's try :52846:  
  
![curl main.php](https://miro.medium.com/max/1400/0*3kGpWBOF0CEuuFFa)  

And we get the private key! With this in hand, we can use john to crack the RSA password. We'll just need to convert to it to the right format first - after some quick research, `ssh2john.py` is the tool for the job.
![john ssh key](https://miro.medium.com/max/1400/0*xLl9SVHckG15LH3t)
  
And we’re in! The user flag is sitting in joanna's home directory.
Next task — get the root flag. Here’s something interesting: `sudo -l`   shows us that we have permissions to run `/bin/nano` with sudo. I got a bit stuck at this point, as I wasn't exactly sure how to use nano to escalate my privileges.
Luckily, I got a hint from colleagues — GTFOBins is a great website that shows how to use Unix binaries to elevate privileges, and we just need slightly alter the payload that we get there.
`sudo /bin/nano /opt/priv`  
`reset; 1>&0 2>&0`  

and that’s it!
![whoami root](https://miro.medium.com/max/1400/0*pvPd4G0zX5ZFw01Z)  
  
The root flag is inside the directory, and we have successfully completed the box!
This box was a nice introduction — it forced me to learn about Apache2 installations and GTFOBins, and it is apparent that these things are going to continually come up. Thanks so much for reading and check back for future HTB write-ups!

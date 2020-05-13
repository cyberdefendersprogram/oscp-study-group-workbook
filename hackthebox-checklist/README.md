# HackTheBox CheckList

Mark Down version of Cherry Tree template by **devzspy** : [https://github.com/devzspy/oscp-certification/blob/master/Note Taking Tools/CherryTree Template/CTF\_template.ctb](https://github.com/devzspy/oscp-certification/blob/master/Note%20Taking%20Tools/CherryTree%20Template/CTF_template.ctb)

This is a template for working on steps to follow when attempting a Hack the Box machine

_Machine Name_: \(10.10.10.ddd\)

## 1. Enumeration

### TCP

### UDP

### Web Services

* Nikto
* gobuster
* WebDav
* CMS

  **Other Services**

* SMB
* SNMP
* DB
* Other

## 2. Exploitation

### Service Exploited:

### Vulnerability Type:

### Exploit POC:

### Description:

### Discovery of Vulnerability

### Exploit Code Used

### Proof\Local.txt File

☐ Screenshot with ifconfig\ipconfig ☐ Submit too OSCP Exam Panel

## 3. Post-Exploitation

### Script Results

### Host Information

### File System

### Running Processes

### Installed Applications

### Users & Groups

### Network

### Scheduled Jobs

### Privilege Escalation

## Goodies

### Hashes

### Passwords

### Proof/Flags/Other

## Software Version

### Software Versions

### Potential Exploits

## Methodology

## Network Scanning

☐ nmap -sn 10.11.1.0/24 ☐ nmap -sL 10.11.1.0/24 ☐ nbtscan -r 10.11.1.0/24 ☐ smbtree

### Individual Host Scanning

☐ nmap --top-ports 20 --open ipaddress ☐ nmap -sS -A -sV -O -p- ipaddress -oA nmap ☐ nmap -sU ipaddress ☐ searchsploit -x --nmap nmap.xml ☐ dig axfr @ipaddress dc

### Service Scanning

```text
WebApp
  ☐   Nikto
  ☐   gobuster -u http://ipaddress -w /usr/share/wordlists/common.txt -s 200,204,301,302,307,403 -r -t 15 -o gobuster.txt
  ☐   wpscan
  ☐   dotdotpwn
  ☐   view source 
  ☐   davtest\cadevar
  ☐   droopscan
  ☐   joomscan
  ☐   LFI\RFI Test

Linux\Windows
  ☐   snmpwalk -c public -v1 ipaddress 1
  ☐   smbclient -L //ipaddress
  ☐   showmount -e ipaddress port
  ☐   rpcinfo
  ☐   Enum4Linux

Anything Else
  ☐   nmap scripts (locate *nse* | grep servicename)
  ☐   hydra
  ☐   MSF Aux Modules
  ☐   Download the software
```

## Exploitation 

☐ Gather Version Numbers ☐ Searchsploit ☐ Default Creds ☐ Creds Previously Gathered ☐ Download the software

## Post Exploitation

```text
Linux
  ☐   linux-local-enum.sh
  ☐   linuxprivchecker.py
  ☐   linux-exploit-suggestor.sh
  ☐   unix-privesc-check.py
  ☐   find / -perm -4000 2>/dev/null | xargs ls -la

Windows
  ☐   wpc.exe
  ☐   windows-exploit-suggestor.py
  ☐   windows_privesc_check.py
  ☐      windows-privesc-check2.exe
```

Priv Escalation ☐ acesss internal services \(portfwd\) ☐ add account

Windows ☐ List of exploits

Linux ☐ sudo su ☐ KernelDB ☐ Searchsploit

Final ☐ Screenshot of IPConfig\WhoamI ☐ Copy proof.txt ☐ Dump hashes ☐ Dump SSH Keys ☐ Delete files

## Log Book


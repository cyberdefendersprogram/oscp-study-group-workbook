# recon

## nmap

```text
nmap -sC -sV -O -v --open -p- -oA nmap/full target.ip
```

## gobuster

```text
gobuster dir -u http://target.ip -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt -o webscan/gobuster-extensions
```




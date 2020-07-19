# OSCP Roadmap

## Quick Facts About The Exam

* You have to root 4 machines out of 5. One machine is buffer overflow, One is easy box, one is insanely hard box, and rest two are medium boxes.
* The Linux machines on OSCP are easier than the hackthebox machines. Usually its an RCE and can be found with LinEnum. For windows boxes it helps to know powershell.
* SQLMap and any commercial tools are not allowed in OSCP.
* You may only use Metasploit modules \(Auxiliary, Exploit, and Post\) or the Meterpreter payload against one single target machine of your choice. Once you have selected your one target machine, you cannot use Metasploit modules \(Auxiliary, Exploit, or Post\) or the Meterpreter payload against any other machines. Metasploit/Meterpreter should not be used to test vulnerabilities on multiple machines before selecting your one target machine, this includes the use of check. You may use Metasploit/Meterpreter as many times as you would like against your one target machine.
* msfvenom is allowed for unlimited use on the exam to create your reverse shell payloads \(shell/reverse\_tcp and shell\_reverse\_tcp\).
* Use Metasploit multi-handler to catch your msfvenom shells is allowed for unlimited use. For this you are allowed to use a staged or non-staged reverse shell payloads, but of course no unlimited Meterpreter.
* pattern\_create and pattern\_offset from Metasploit are allowed for unlimited use to allow you to create buffer overflow payloads.

## Roadmap

This is a list of topics you should be familiar with:

* 

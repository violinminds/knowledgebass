# Operating System Protection Rings

## What is it?

> [[ext] OS Protection rings / Hierarchical protection domains](https://en.wikipedia.org/wiki/Protection_ring)

## Microsoft Windows: inspecting protected processes...

... or why not even the system's Win32 APIs can fetch information about some processes, even if calling them from the `NT Authority\System` account.

### A user process example...

```powershell
# from powershell
Get-WmiObject Win32_Process -Filter "name = 'explorer.exe'" | Select-Object processid,processname,path,commandline
```

Results in something like:

```log
ProcessId ProcessName  Path                    CommandLine
--------- -----------  ----                    -----------
    10752 explorer.exe C:\WINDOWS\Explorer.EXE C:\WINDOWS\Explorer.EXE
```

### ... a kernel/protected process example ...

```powershell
# from powershell
Get-WmiObject Win32_Process -Filter "name = 'csrss.exe'" | Select-Object processid,processname,path,commandline
```

Results in:

```log
ProcessId ProcessName Path CommandLine
--------- ----------- ---- -----------
      824 csrss.exe
     1148 csrss.exe
```

### ... and The Solution

> [[ext] Debugging Protected Processes](https://itm4n.github.io/debugging-protected-processes/) ([archived](https://web.archive.org/web/*/https://itm4n.github.io/debugging-protected-processes/))

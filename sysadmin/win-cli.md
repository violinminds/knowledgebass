# Windows Command Line

## References

- https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands
- https://ss64.com/
- https://ss64.com/nt/
- https://ss64.com/ps/

## Useful thingies

Useful commands.

### See current Logon sessions

RDP sessions are included.

```powershell
PS> query session
    SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE
    services                                    0  Disc
    >console           u6                        1  Active
    rdp-tcp#0         aet                       3  Active
    rdp-tcp                                 65536  Listen
```

### Terminate a Logon session

Disconnects the user and Terminates the logon session.

```powershell
# use "query session" as seen above to get the session ID, the run: logoff.exe <ID>
PS> logoff 3
```

### Control Panel, system dialogs, Microsoft Management Console

> - https://learn.microsoft.com/en-us/windows/win32/shell/executing-control-panel-items
> - https://www.itechtics.com/control-panel-applets-cpl/ ([archived](https://web.archive.org/web/20230331110750/https://www.itechtics.com/control-panel-applets-cpl/))
> - https://4sysops.com/wiki/list-of-ms-settings-uri-commands-to-open-specific-settings-in-windows-10/ ([archived](https://web.archive.org/save/https://4sysops.com/wiki/list-of-ms-settings-uri-commands-to-open-specific-settings-in-windows-10/))

- `appwiz.cpl`: opens "Programs and Features"
- `ncpa.cpl`: opens "Network Connections"
- `systempropertiesadvanced`: opens "System Properties", "Advanced" tab
- `services.msc`: opens "Services"
- `compmgmt.msc`: opens "Computer Management"
- `devmgmt.msc`: opens "Device Manager"
- `diskmgmt.msc`: opens "Disk Management"
- `gpedit.msc`: opens "Local Group Policy Editor"
- `lusrmgr.msc`: opens "Local Users and Groups"
- `mmc`: opens Microsoft Management Console
- `mstsc`: opens "Remote Desktop Connection"
- `taskmgr`: opens Task Manager
- `mspaint`: opens Paint
- `slui`: opens "Windows Activation Center"
- `winver`: opens "About Windows"
- `control`: opens Control Panel

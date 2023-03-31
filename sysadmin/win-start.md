# Starting applications on Windows

> https://learn.microsoft.com/en-us/windows/win32/shell/app-registration

## What can Windows invoke?

Windows can invoke files having an extension present in the `PATHEXT` environment variable, which might be similar to the following:

```powershell
PS> echo $env:PATHEXT
.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.PY;.PYW
```

Also, Windows will try to infer the extension, if not specified, by parsing `PATHEXT` in order, one extension at a time, choosing the first existing file with that extension, only **after** choosing the first valid file location (see section below).

For example, if both `x.exe` and `x.cmd` exist in the current folder, `x.exe` is executed when running just `x`.

If `x.cmd` and `x.exe` exist both in the current directory, **and** in a directory in the system `PATH`, then the `x`s are executed in the following order:

- `x.exe` (current folder)
- `x.cmd` (current folder)
- `c:\folder\in\path\x.exe`
- `c:\folder\in\path\x.cmd`

### Powershell peculiarity

Powershell runs files in the current directory only if the name is preceded by `.\`, or if using `start` instead of `&`:

```powershell
x                     # error
.\x                   # works
start -nonewwindow x  # works
```

For more information run: `get-help about_Command_Precedence`

## Where does Windows look for apps?

> When the [ShellExecuteEx](https://learn.microsoft.com/en-us/windows/desktop/api/Shellapi/nf-shellapi-shellexecuteexa) function is called with the name of an executable file in its _lpFile_ parameter, there are several places where the function looks for the file. We recommend registering your application in the **App Paths** registry subkey. Doing so avoids the need for applications to modify the system PATH environment variable.
>
> - The file is sought in the following locations:
>
> - The current working directory.
> - The **Windows** directory only (no subdirectories are searched).
> - The **Windows\System32** directory.
> - Directories listed in the PATH environment variable.
> - Recommended: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths**

Please note that the `PATH` environment variable contains multiple paths, separated by `;`. If a path itself contains the semicolon, the path is enclosed in double quotes (eg. `c:\windows;"c:\path;with;semicolon"`).

For example, if you have the [Brave browser](https://brave.com/) installed, **you can** open it with:

```powershell
start brave
```

but if you look for it via `Get-Command` (powershell) or `where.exe`, you will not find it:

```powershell
PS> Get-Command brave
Get-Command : The term 'brave' is not recognized as a name of a cmdlet, function, script file, or executable program.
Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
At line:1 char:1
+ Get-Command brave
+ ~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : ObjectNotFound: (brave:String) [Get-Command], CommandNotFoundException
+ FullyQualifiedErrorId : CommandNotFoundException,Microsoft.PowerShell.Commands.GetCommandCommand

PS> where.exe brave
INFO: Could not find files for the given pattern(s).
```

This is because Brave registers itself in the system registry under the `App Paths` key:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths\brave.exe]
@="C:\\Program Files\\BraveSoftware\\Brave-Browser\\Application\\brave.exe"
"Path"="C:\\Program Files\\BraveSoftware\\Brave-Browser\\Application"
```

### Powershell peculiarity

Powershell doesn't treat the current working directory as first possible location of executables.

If `x.exe` exists both in the current directory and in a directory in the system `PATH`, when running `x.exe`, the latter takes precedence, for example.

For more information run: `get-help about_Command_Precedence`

## Verbs, file associations, perceived types

> TODO

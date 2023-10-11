# PowerShell knowledge

Useful notions and examples for Windows PowerShell and PowerShell "Core" (`pwsh`).

## Snippets

### Setup allowed web security protocols

This only allows `Tls12` and `Tls13` connections.

It is best to put this command also in your `$PROFILE` file.

> - `Tls 1.0` and `Tls 1.1` are deprecated as of [RFC 8996](https://datatracker.ietf.org/doc/html/rfc8996) id est March 2021
> - All `SSL` versions are deprecated as of [RFC 7568](https://datatracker.ietf.org/doc/html/rfc7568) id est June 2015

```powershell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls12,Tls13'
# or a bit shorter
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]'Tls12,Tls13'
```

### Run a remote (online) script, version 1

NB: the version #2 of this script is preferred; see why below.

> Also see snippets:
>
> - [Alternative script download method](#alternative-script-download-method)
> - [Setup allowed web security protocols](#setup-allowed-web-security-protocols)

```powershell
# short version one-liner:
$u = 'https://cdn.jsdelivr.net/gh/aetonsi/misc/scripts/setup-gitbash-environment/main.ps1'; irm $u | iex
# extended version one-liner:
$URL = 'https://cdn.jsdelivr.net/gh/aetonsi/misc/scripts/setup-gitbash-environment/main.ps1'; Invoke-RestMethod $URL | Invoke-Expression
```

### Run a remote (online) script, version 2

This script respects any `#Requires` directive and removes the need for any check about administrator privileges and whatnot.

> Also see snippets:
>
> - [Alternative script download method](#alternative-script-download-method)
> - [Setup allowed web security protocols](#setup-allowed-web-security-protocols)

```powershell
# short version one-liner:
$u = 'https://cdn.jsdelivr.net/gh/aetonsi/misc/scripts/setup-gitbash-environment/main.ps1'; [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]'Tls12,Tls13'; Set-ExecutionPolicy b -s p -f; irm $u > ($f = "$([IO.Path]::GetTempPath())$([GUID]::NewGuid()).ps1"); & $f; ri $f -f
# extended version one-liner:
$URL = 'https://cdn.jsdelivr.net/gh/aetonsi/misc/scripts/setup-gitbash-environment/main.ps1'; [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]'Tls12,Tls13'; Set-ExecutionPolicy Bypass -Scope Process -Force; Invoke-RestMethod $URL > ($File = "$([System.IO.Path]::GetTempPath())$([GUID]::NewGuid()).ps1"); & $File; Remove-Item $File -Force
# extended version line by line:
$URL = 'https://cdn.jsdelivr.net/gh/aetonsi/misc/scripts/setup-gitbash-environment/main.ps1'
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls12,Tls13'
Set-ExecutionPolicy Bypass -Scope Process -Force
Invoke-RestMethod $URL > ($File = "$([System.IO.Path]::GetTempPath())$([GUID]::NewGuid()).ps1")
& $File
Remove-Item $File -Force
```

### Alternative script download method

This replaces `irm`/`Invoke-RestMethod`.

```powershell
([System.Net.WebClient]::New()).DownloadString($URL)
# or a bit shorter
([Net.WebClient]::New()).DownloadString($URL)
```

### `Select-Object` with custom expression

```PowerShell
Get-Process |
    Select-Object -First 10 id,
        processname,
        @{label = 'ExecutableType'; expression = { [System.IO.Path]::GetExtension($_.Path) } },
        path |
    Format-Table
```

### `switch` with complex expressions

This reverse switch works in almost every language (at least those with c-like syntax).

```powershell
switch ($true) {
    (($PSVersionTable.PSEdition -eq 'Core') -and ($PSVersionTable.PSVersion -ge 7)) { Write-Output "it's pwsh >= 7.x" ; break }
    (($PSVersionTable.PSEdition -eq 'Core') -and ($PSVersionTable.PSVersion -lt 7)) { Write-Output "it's pwsh 6.x" ; break }
    ($PSVersionTable.PSEdition -eq 'Desktop') { Write-Output "it's winposh" ; break }
    ($true) { "it's true" }
    default { Write-Output 'else' }
}
```

### Debug breakpoints

> Run `man about_Debuggers` for information about debugging.

The following is a simple setup to trigger a debug session from within a script, like php's `xdebug_break()`, then allow for fiddling with the current powershell session.
You can even access the session's variables current values.
This session is more or less equivalent to running a powershell script with the `-noexit` argument, except that you can use this method in the middle of a script.

```PowerShell
### debug-helper.ps1

# define once, then SOURCE the script (see below)
function debug_break() {}
function debug_breakpoints_setup() {
    Get-PSBreakpoint | Remove-PSBreakpoint
    Set-PSBreakpoint -Command debug_break >$null
}
```

```PowerShell
### some-script.ps1

# call at the start of each script
. ./debug-helper.ps1 # dot-source the script (man about_Scripts); if importing a module instead, the debug session will not have access to the current scope
debug_breakpoints_setup

# ... your script ...
$xxx = Get-Random -Minimum 0 -Maximum 1000
Write-Output "`$xxx = $xxx"
debug_break # this effectively starts the debug session here; you can test and access $xxx inside the session
Write-Output "after debug"
# ...
```

You can then use hotkeys to control the debug session (run `?` in a debug session for more info):

- `c`: continues execution until the next breakpoint
- `s`: steps into the next statement then stops
- `v`: steps over the next statement, skipping invocations, then stops
- `k`: displays current call stack
- `q`: stops the script execution, like an `Exit` statement
- `?`: display debugging help

* [[ext] classes](https://github.com/dahlbyk/posh-git/blob/master/src/PoshGitTypes.ps1)
* [[ext] Custom Tab completition](https://github.com/dahlbyk/posh-git/blob/master/src/GitTabExpansion.ps1)

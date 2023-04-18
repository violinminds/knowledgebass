# PowerShell knowledge

Useful notions and examples for Windows PowerShell and PowerShell "Core" (`pwsh`).

## Notions

### Debug breakpoints

> Run `man about_Debuggers` for information about debugging.

The following is a simple setup to trigger a debug session from within a script, like php's `xdebug_break()`, then allow for fiddling with the current powershell session.
You can even access the session's variables current values.

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

## Examples

- [[ext] classes](https://github.com/dahlbyk/posh-git/blob/master/src/PoshGitTypes.ps1)
- [[ext] Custom Tab completition](https://github.com/dahlbyk/posh-git/blob/master/src/GitTabExpansion.ps1)

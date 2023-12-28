# snippets.md

## Tray menu

How to setup a simple tray icon menu accessible by right-clicking the AHK script's icon.

NB: the callback functions **have** to have 3 arguments (`ItemName, ItemPos, MyMenu`).

```autohotkey
#SingleInstance
#Warn All,msgbox
Persistent()

A_TrayMenu.Delete()

A_TrayMenu.Add("Open Clipboard History", fn_hotkey)
A_TrayMenu.Add("Run", fn_hotkey)
A_TrayMenu.Add()
A_TrayMenu.Add("Open VS Code", fn_code)
A_TrayMenu.Add("ahk Reload()", fn_reload)
A_TrayMenu.Add("Quit.", fn_quit)


fn_hotkey(ItemName, ItemPos, MyMenu) {
    Switch ItemPos {
    Case 1:
        try Send('#v')
    Case 2:
        Send('#r')
    Default:
        msgbox('fn_hotkey err')
        ExitApp(1)
    }
}

fn_code(ItemName, ItemPos, MyMenu) {
    RunWait('cmd /c code')
}

fn_reload(ItemName, ItemPos, MyMenu) {
    msgbox('reloading script...')
    Reload()
}

fn_quit(ItemName, ItemPos, MyMenu) {
    ExitApp(0)
}
```

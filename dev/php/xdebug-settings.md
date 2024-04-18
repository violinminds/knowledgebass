# Settings for php's Xdebug

## php's binary command line arguments

- `-dxdebug.mode=off`: disables the execution of Xdebug for the current command, eg: `php "-dxdebug.mode=off" -r "echo 'test';"`; note that quoting might be necessary (for example, on Windows' cmd.exe)

## Xdebug settings in `php.ini`

### modern php versions (>5.x)

```ini
[Xdebug]
zend_extension=xdebug
xdebug.log=PHP_BINARY"/../xdebug_errors.log" ; WINDOWS ONLY - see php-ini.md for explanation
; xdebug 3.x
xdebug.mode=debug
xdebug.start_with_request=yes
; xdebug 2.x
xdebug.remote_port = 9003
xdebug.client_port = 9003
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```

### older php versions (<=5.x)

`zend_extension=xdebug` won't work, you have to point to the dll. You have some options:

- use a path relative to the php binary:

  ```ini
  zend_extension=./ext/php_xdebug.dll
  ```

- use an absolute path:

  ```ini
  zend_extension=c:\php\ext\php_xdebug.dll
  ```

- add the dll folder to the system `PATH` (eg. in powershell `[Environment]::SetEnvironmentVariable("Path", "$env:path;c:\php\ext", "user")`), then just specify the name of the dll:

  ```ini
  zend_extension=php_xdebug.dll
  ```

## Settings for VS Code's `launch.json`

```jsonc
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for Xdebug",
      "type": "php",
      "request": "launch",
      "port": 9003,
      "xdebugSettings": {
        // customize
        "max_children": 128,
        "max_data": 512,
        "max_depth": 6,
      },
    },
  ],
}
```

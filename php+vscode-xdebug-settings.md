# Settings for php's Xdebug
## `php.ini`
```ini
[Xdebug]
zend_extension=xdebug
xdebug.mode=debug
xdebug.start_with_request = yes
```

## VS Code's `launch.json`
```jsonc
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "xdebugSettings": { // optional but recommended
                "max_children": 128,
                "max_data": 512,
                "max_depth": 6
            }
        },
    ]
}
```

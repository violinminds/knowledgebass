# `php.ini` settings

Useful settings for `php.ini` files.

- if you need to reference a php constant, just write it without quotes: `CONSTANT_NAME`; eg. `PHP_BINARY`
- if you need to reference another **previously** declared ini setting or an Environment variable, use `${name}`; eg. `${extension_dir}` or `${PATH}`
- if you need to use a path relative to some other path stored in a php constant, env var, or ini setting, just append your path as a string to the reference; eg. `TEMP"/php.log"` or `${extension_dir}"/something"` (working as of today 2023-02-10, php v8.2.2)
  - **_hack for php on Windows_**: you can reference the **directory containing a file** by starting your string with `/../`; eg. `PHP_BINARY"/../logfile.log"` will result in a path relative to the folder containing the php binary (see usage example below); this is possible because the ini parser/filesystem apis will blindly delete the previous portion of the path whenever it encounters a `/..`, so `c:\php\php.exe\..\folderX` will result in `c:\php\folderX`
    - \#TODO find an equivalent for linux - you can use an env var as alternative

## Various

```ini
error_log = PHP_BINARY"/../php_errors.log" ; WINDOWS ONLY: php will always use the SAME log file located in the directory of the php executable
error_log = php_errors.log ; php will create a log file in EVERY directory where php is invoked (starting directory)
error_log = syslog ; logs to the system log (Event Viewer->Windows Logs->Application on windows, syslog on linux)

memory_limit = 2G ; increase to your necessity

display_errors = stderr ; print errors to the stderr stream instead of stdout - works only for CLI environments

extension_dir = "ext" ; necessary on windows

curl.cainfo="C:\path\to\cacert.pem" ; if php cannot find or use the system's certificates
openssl.cafile="C:\path\to\cacert.pem" ; if php cannot find or use the system's certificates
```

## Extensions

```ini
; general purpose
extension=mbstring
extension=intl
extension=sodium
; remote connections
extension=curl
extension=openssl
extension=sockets
; filesystem
extension=fileinfo
extension=exif
extension=zip
; databases
extension=odbc
extension=pdo_mysql
extension=pdo_odbc
extension=pdo_pgsql
extension=pdo_sqlite
extension=pgsql
extension=sqlite3
```

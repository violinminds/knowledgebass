# snippets.md

## `PDO` connection to a MSSQL Server

```php
$addr = '127.0.0.1\SQLEXPRESS';
$usr = 'sa';
$pwd = readline('Enter Password: ');
$obj = new PDO("sqlsrv:Server=$addr", $usr, $pwd);
echo $obj->query('SELECT @@VERSION')->fetch(PDO::FETCH_COLUMN);
```

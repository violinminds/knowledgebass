# Microsoft SQL Server

Please note that the guides below require [SQL Server Management Studio (SSMS)](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) and have been tested with SSMS v19.1.56.0 and SQL Server 2022 (RTM-CU8, KB5029666, v16.0.4075.1, X64).

## How to enable `sa` user

In SQL Management Studio:

1. `security` > `logins` > `sa` > [double click to open properties]
2. `general` > `password` > [set a password]
3. `status` > `login` > [set to `Enabled`]
4. `[ok]`

## How to enable TCP-IP login

In SQL Server Configuration Manager:

1. `SQL Server Network Configuration` > `Protocols for SQLEXPRESS` > [double click TCP/IP]
2. `Protocol` > [`Enabled` YES]
3. `IP Addresses` > `IP1`,`IP10`,etc > [`Active` YES, `Enabled` YES, `TCP Port` 1433]
4. `[ok]`

## How to enable mixed auth

Execute query in SQL Management Studio:

```sql
USE [master]
GO
EXEC xp_instance_regwrite
    N'HKEY_LOCAL_MACHINE',
    N'Software\Microsoft\MSSQLServer\MSSQLServer',
    N'LoginMode',
    REG_DWORD,
    2; -- sql+windows auth
GO
```

## How to enable browser service

In `services.msc`:

1. set SQLBrowser service startup to `Automatic`
2. start SQLBrowser service

## How to enable login for other apps

This is a quick guide on how to allow other database management tools (eg. [DBeaver](https://dbeaver.io/)) to connect to your locally installed SQL Server instance.

1. enable/create a sql server user (eg. `sa`) (see dedicated section)
2. enable mixed auth (see dedicated section)
3. enable tcp-ip login (see dedicated section)
4. enable browser service (see dedicated section)
5. **stop** and **start** SQL Server and SQL Server Browser services
6. connect with these credentials

  ```text
    host 127.0.0.1\SQLEXPRESS
    port 1433
    user sa
    pass <selected password>
  ```

Please note: server name `127.0.0.1\SQLEXPRESS` should be what you see in SQL Server Management Studio's object explorer and will probably vary depending on the server version.

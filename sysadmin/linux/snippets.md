# Linux snippets

## Get back to the install log after an SSH section drop

```shell
sudo tail -f /var/log/apt/term.log
```

## Create a multiline string variable

Variable name in the example is `smbcfg`.

This syntax is called [HEREDoc](https://en.wikipedia.org/wiki/Here_document).

```shell
read -r -d '' smbcfg << hd
[shared]
   comment= Shared Folder
   path = /usr/shared
   browseable = Yes
   writeable = Yes
   only guest = no
   create mask = 0777
   directory mask = 0777
   public = no
   force user = pi
hd
```

### sudo-append to a file

Append to a file which would require SU privileges.

In the example, `smbcfg` is just a variable.

```shell
echo "$smbcfg" | sudo tee -a /etc/samba/smb.conf
```

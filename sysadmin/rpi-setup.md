# Raspberry PI Setup

## Sources

- <https://raspberrypi.stackexchange.com/a/7647>
- <https://raspberrypi.stackexchange.com/a/139930>
- <https://github.com/neutrinolabs/xrdp/issues/2053>
- <https://forums.raspberrypi.com/viewtopic.php?t=274439>

### 1. Update things

```shell
sudo apt update
sudo apt upgrade
```

### 2. Setup SSH banner

```shell
echo "Hello world! Successfully logged into RPI" | sudo tee -a /etc/sshbanner
echo "Banner=/etc/sshbanner" | sudo tee -a /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### 3. `raspi-config`

```shell
sudo raspi-config
```

### 4. Config NetBIOS name

... by installing Samba + avahi-daemon.

```shell
sudo apt install samba samba-common-bin
sudo smbpasswd -a pi
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
echo "$smbcfg" | sudo tee -a /etc/samba/smb.conf

sudo apt-get install avahi-daemon
sudo systemctl enable avahi-daemon
sudo /etc/init.d/avahi-daemon restart
```

### 5. Setup RDP

```shell
sudo apt install xrdp
sudo deluser pi render # necessary or else you get a black screen on some RPI-Os versions
```

### 6. (optional) Setup [TinyPilot](https://github.com/tiny-pilot/tinypilot)

Follow [instructions here](https://github.com/tiny-pilot/tinypilot?tab=readme-ov-file#simple-installation) for up-to-date commands.

```shell
curl \
  --silent \
  --show-error \
  https://raw.githubusercontent.com/tiny-pilot/tinypilot/master/get-tinypilot.sh | \
    bash - && \
  sudo reboot
```

#### 6.1 add HTTP basic auth to TinyPilot

```shell
# https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-14-04
sudo sh -c "echo -n 'sammy:' >> /etc/nginx/.htpasswd"
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
cat /etc/nginx/.htpasswd
```

... in `/etc/nginx/conf.d/tinypilot.conf`:

```config
server {
    location / {
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

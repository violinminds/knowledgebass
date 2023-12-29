# Android Debug Bridge (adb) snippets

## Poweroff the phone

```shell
adb shell reboot -p
```

## Kill the running ADB server

```shell
adb kill-server
```

## List devices

```shell
adb devices
```

## Run a command specifically for the running emulator or the connected physical device

```shell
adb -d <command> # command for device
adb -e <command> # command for emulator
adb -s <deviceid> # specify target
# or just set an environment variable ANDROID_SERIAL='<deviceid>'
```

## Shell commands

### Open the shell

```shell
adb shell
```

### (Un)Install an app

```shell
adb install file.apk
adb uninstall com.app.www
```

### Start (open) an app

```shell
adb shell monkey -p com.app.www 1 # starts main activity (opens the app, usually)
```

## Wifi ADB

```powershell
# 192.168.1.34 is the LAN IP of the android device
adb pair 192.168.1.34:38437
adb connect 192.168.1.34:35645
```

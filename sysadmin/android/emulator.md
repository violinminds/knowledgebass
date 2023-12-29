# Android Emulator

References

> - <https://developer.android.com/guide/topics/manifest/uses-sdk-element#ApiLevels>
> - <https://apilevels.com/>

## Setup

One time setup.
In the examples, "Pixel4_api30_android11_x64" is the name of the emulator being created.
In the examples, android api level 30 is used, which is android 11 (see references above).

All commands are in Powershell syntax.

### Install tools, platform, system image

```Powershell
sdkmanager platform-tools
sdkmanager "build-tools;30.0.3"
sdkmanager "platforms;android-30"
sdkmanager "system-images;android-30;google_apis;x86_64"
```

### Create emulator

```powershell
# avdmanager create avd -n <name> -k "system-images;android-<api level>;google_apis;x86_64"
avdmanager create avd -n Pixel4_api30_android11_x64 -k "system-images;android-30;google_apis;x86_64"

# check if all is well
emulator -list-avds
```

### `config.ini` useful settings

Add the following settings to the emulator's configuration file, located at:

> `<$env:USERPROFILE>\.android\avd\<emulator name>\config.ini`

So, in the example:

> `C:\Users\username\.android\avd\Pixel4_api30_android11_x64.avd\config.ini`

- `nhw.lcd.width = 720` and `nhw.lcd.height = 1280` set the resolution to 720p
- `nhw.lcd.density = 240` sets DPI to an acceptable level
- `nhw.keyboard = yes` is needed for full functioning of the computer keyboard (eg. copy-pasting)

## Run the emulator

Just run this command every time you have to start the emulator.

```powershell
# emulator -avd <emulator name>
emulator -avd Pixel4_api30_android11_x64
```

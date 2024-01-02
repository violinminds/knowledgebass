# Windows World

## Snippets

### Change Display projection settings via command line

From <https://superuser.com/a/1116541/1744807>.

> - `/internal` calls the internal screen (your primary display)
> - `/external` changes to the external screen (im not sure how well it handles when there is more than 1 screen)
> - `/clone` duplicates displays.
> - `/extend` switches to extended settings.

```powershell
C:\Windows\System32\DisplaySwitch.exe /internal
C:\Windows\System32\DisplaySwitch.exe /external
C:\Windows\System32\DisplaySwitch.exe /clone
C:\Windows\System32\DisplaySwitch.exe /extend
```

# win-openssh.md

> https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui

Setup procedure for using OpenSSH client/server on Windows.

## Client

```powershell
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

## Server

```powershell
# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

# Start the sshd service
Start-Service sshd

# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'

# Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

# Key based authentication

> https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement

## Generate key for client

- "comment" = comment for user friendly listing via `ssh-add -l` and/or use with git (see below)
- "filepath" = path to key file (eg. in powershell, `$env:USERPROFILE\.ssh\my-key`)

> REMEMBER TO SET PASSPHRASE TO THE KEY FILE!!!

```powershell
ssh-keygen -C comment -f filepath
```

## Allow key on server

### Manually

1. client side: copy the **public** key contents to clipboard (manually or via `type "c:\users\username\.ssh\my-key.pub" | clip`)

2. server side: add public key to server's authorized keys by editing `C:\ProgramData\ssh\administrators_authorized_keys` and appending your public key
3. server side: grant permissions to `Administrators` group and `SYSTEM` user for the `administrators_authorized_keys` file

### Via script

Execute this script on the client, after adjusting the variables values.

```powershell
$publicKeyPath = "$env:USERPROFILE\.ssh\my-key.pub"
$serverUsername = "user"
$serverHostname = "remote.violinminds.com"
$serverPort = "22"

# Get the public key file generated previously on your client
$authorizedKey = Get-Content -Path $publicKeyPath

# Generate the PowerShell to be run remote that will copy the public key file generated previously on your client to the authorized_keys file on your server
$remotePowershell = "powershell Add-Content -Force -Path $env:ProgramData\ssh\administrators_authorized_keys -Value '$authorizedKey';icacls.exe ""$env:ProgramData\ssh\administrators_authorized_keys"" /inheritance:r /grant ""Administrators:F"" /grant ""SYSTEM:F"""

# Connect to your server and run the PowerShell using the $remotePowerShell variable
ssh ${serverUsername}@${serverHostname} -p${serverPort} $remotePowershell
```

## SSH Agent setup

1. setup the agent service to automatically start with Windows (see above); start the service
2. add **every** key to the agent, one by one, via `ssh-add`; eg. in powershell: `ssh-add $env:USERPROFILE\.ssh\my-key` (private keys don't have ".pub" extension) (passphrase will be required)
3. list added keys via: `ssh-add -l`

## Github setup

### Common setup

0. !!! When installing [git for windows](https://gitforwindows.org/), remember to select "Use external OpenSSH" !!!
   ![image](https://user-images.githubusercontent.com/18366087/212734447-56f811dd-6bdd-46c4-bc10-8032b7ef2387.png)
1. create keys for the client (see above)
   - for authentication, create a key with comment **containing** "github-auth", eg. in powershell `ssh-keygen -C github-auth -f "$env:USERPROFILE\.ssh\github-auth"`
   - for signing, create a key with comment **containing** "github-signing", eg. in powershell `ssh-keygen -C github-signing -f "$env:USERPROFILE\.ssh\github-signing"`
2. add keys to your profile @ https://github.com/settings/keys (copy-paste both the public keys contents)

### Authentication (cloning / pulling / pushing / etc)

> If you did **not** select "Use external OpenSSH" during the git installation, run: `git config --global core.sshcommand C:/Windows/System32/OpenSSH/ssh.exe` (**NOT** guaranteed to work)

Test auth via: `ssh -T git@github.com`, it should print: "Hi _username_! You've successfully authenticated, but GitHub does not provide shell access.".

Now you should be able to clone a repository via: `git clone git@github.com:violinminds/knowledgebass.git` (get link in the repository page on Github by clicking the "<> Code" button and and selecting "SSH" in the clone section).

### Commits signing

Configure git to **always** sign commits with your ssh key with comment containing "github-signing":

- instruct git to use ssh instead of gpg: `git config --global gpg.format ssh`
- force signing of all commits: `git config --global commit.gpgsign true`
- force signing of all tags: `git config --global tag.gpgsign true`
- command to retrieve public key from agent: `git config --global gpg.ssh.defaultKeyCommand "cmd /c ssh-add -L | findstr github-signing"` (pay attention to escaping the "|" character) <details><summary>// TODO</summary>fix procedure to allow other hosts aside github eg? `[includeIf "hasconfig:remote.*.url:**github.com**"]`</details>
- this should be the result in your `.gitconfig` file (`$env:USERPROFILE/.gitconfig`):

```log
# ... rest of the file ...
[gpg]
    format = ssh
[commit]
    gpgsign = true
[tag]
    gpgsign = true
[core]
    # for AUTH (not necessary if 'use external openssh' selected during git for windows setup)
    # sshcommand = C:/Windows/System32/OpenSSH/ssh.exe
[gpg "ssh"]
    # for SIGNING
    defaultKeyCommand = cmd /c ssh-add -L | findstr github-signing
```

Now you can commit as usual, or by explicitly signing the commit via the `-S` parameter: `git commit -S -m "message"`

## Troubleshooting

- if you get the warning "@ WARNING: UNPROTECTED PRIVATE KEY FILE! @" when trying to use the keys, fix the private key(s) file(s) permissions to allow FULL CONTROL to the current user, `SYSTEM` and the `Administrators` group
- if a repository was already cloned via HTTPS and you want to start working via SSH, change its remote via: `git remote set-url origin git@github.com:violinminds/knowledgebass.git`
- if you get the error "Permission denied (publickey). fatal: Could not read from remote repository.", to debug, you can set the `sshCommand` in the git config to be more verbose: `git config --global core.sshcommand ssh -v` (use `-vvv` to be even more verbose)

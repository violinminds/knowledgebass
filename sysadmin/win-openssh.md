# OpenSSH for Windows

> - https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui
> - https://github.com/PowerShell/Win32-OpenSSH

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

## Test connection

Test connection via `ssh -T <ssh-host>` (replace "ssh-host" with your server's ip or URI).

If you wish you can add a welcome banner to the server so that you see a custom message every time you log onto it. Just edit the server's config (`c:\ProgramData\ssh\sshd_config` on the server's filesystem) and add/uncomment the `Banner` line specifying a path to a text file (eg. `Banner c:\my_sshd_banner.txt`). Then restart the ssh server to apply the new setting, and test the connection again; you will see your banner upon connecting.

## Github setup

### Common setup

0. !!! When installing [git for windows](https://gitforwindows.org/), remember to select "Use external OpenSSH" !!!
   ![image](https://user-images.githubusercontent.com/18366087/212734447-56f811dd-6bdd-46c4-bc10-8032b7ef2387.png)
1. create keys for the client (see above)
   - for authentication, create a key with comment **containing** "git-auth", eg. in powershell `ssh-keygen -C git-auth -f "$env:USERPROFILE\.ssh\git-auth"`
   - for signing, create a key with comment **containing** "git-signing", eg. in powershell `ssh-keygen -C git-signing -f "$env:USERPROFILE\.ssh\git-signing"`
2. add keys to your profile @ https://github.com/settings/keys (copy-paste both the public keys contents)

### Authentication (cloning / pulling / pushing / etc)

Test auth via: `ssh -T git@github.com`, it should print: "Hi _username_! You've successfully authenticated, but GitHub does not provide shell access.".
If that's not the case and you get the error "git@github.com: Permission denied (publickey).", debug with the `-v` or `-vvv` switches ("verbose"): `ssh -vvvT git@github.com`. See the Troubleshooting section for more help.

Now you should be able to clone a repository via: `git clone git@github.com:violinminds/knowledgebass.git` (get link in the repository page on Github by clicking the "<> Code" button and and selecting "SSH" in the clone section).

#### Visual Studio config

Thanks to the global git settings, Visual Studio should be already working correctly.

#### Visual Studio Code config

To configure vscode to use SSH instead of HTTPS, add `"github.gitProtocol": "ssh", "remoteHub.gitProtocol": "ssh"` to your settings.json file.

### Commits signing

Configure git to **always** sign **Github** commits with your ssh key identified by the comment containing "git-signing" (run all commands in powershell):

> OPTIONAL (**not** recommended): if you want, you can use a custom config file **only** for host "git@github.com": `git config --global "includeIf.hasconfig:remote.*.url:git@github.com**/**.path" .github.gitconfig`. If you do so, run the following commands replacing `--global` with `--file $env:USERPROFILE/.github.gitconfig`. PLEASE NOTE: if you do so, commits will be signed **only** after the "git@github.com" remote has been added, so initializing new repositories and immediately committing to them will create non-signed commits!

- instruct git to use ssh instead of gpg: `git config --global gpg.format ssh`
- force signing of all commits: `git config --global commit.gpgsign true`
- force signing of all tags: `git config --global tag.gpgsign true`
- command to retrieve public key from agent: `git config --global gpg.ssh.defaultKeyCommand 'cmd /c "C:\\Windows\\System32\\OpenSSH\\ssh-add.exe" -L | findstr git-signing'`

Now you can commit as usual, or by explicitly signing the commit via the `-S` parameter (`git commit -S -m "message"`); commits will always be signed by default.

#### Signature verification

You can (and should) verify whether your commits have been correctly signed or not.

##### On Github

Open the repository commits history (`<repo url>/commits`, eg. https://github.com/aetonsi/pwsh__Utils/commits/). If a commit is signed correctly with your registered signing key, you will see a green "Verified" label for the commit:
![image](https://user-images.githubusercontent.com/18366087/213741833-1ea2a8d5-4987-4df5-a4cd-8912ec49e199.png)

##### Locally

Run: `git log --show-signature`. You will see that the commits appear as unverified and you will get an error:

```log
error: gpg.ssh.allowedSignersFile needs to be configured and exist for ssh signature verification
commit 605862b7c393385266419b46f2409a5e019391c2 (HEAD -> master, origin/master)
No signature
Author: aetonsi <18366087+aetonsi@users.noreply.github.com>
Date:   Fri Jan 20 15:24:25 2023 +0100

    add util confirm
error: gpg.ssh.allowedSignersFile needs to be configured and exist for ssh signature verification

...
```

Just as Github knows a signature is valid by checking in its list of authorized keys, you also have to make a list of authorized keys for your local git installation. To do so, run the following commands (powershell):

```powershell
git config --global gpg.ssh.allowedSignersFile "$env:USERPROFILE\.ssh\allowed_signers"

echo "$(git config --get user.email) $(type $env:USERPROFILE\.ssh\git-signing.pub)" >> "$env:USERPROFILE\.ssh\allowed_signers"
```

The file structure is simple: it simply contains a list of `<email> <public key>` rows, each one representing an authorized user.

Then you can verify your commits' signatures again and you will get "Good git signature for ...":

```log
commit 605862b7c393385266419b46f2409a5e019391c2 (HEAD -> master, origin/master)
Good "git" signature for 18366087+aetonsi@users.noreply.github.com with RSA key SHA256:5GiMEA805OT3GRyPCR+OJqiiPFvDomh7Kr3NGFUeSxI
Author: aetonsi <18366087+aetonsi@users.noreply.github.com>
Date:   Fri Jan 20 15:24:25 2023 +0100

    add util confirm

...
```

#### Visual Studio config

Thanks to the global git settings (`gpg.ssh.defaultKeyCommand` and `core.sshCommand` in particular), Visual Studio should be already working correctly.

#### Visual Studio Code config

If you want to be sure to enforce commit signing in vscode, add `"git.enableCommitSigning": true` to your settings.json file.

### Enforce SSH transport protocol for git

To disallow any transport protocol **except** SSH (recommended for consistency), run:

```powershell
git config --global protocol.http.allow never
git config --global protocol.https.allow never
git config --global protocol.git.allow never
git config --global protocol.file.allow never
git config --global protocol.ssh.allow always
```

Please note: some platforms (eg. Heroku) allow access only via a single protocol (eg. HTTPS). For those repositories, you have to allow the protocol you need: `git config protocol.https.allow always`. You will never be able to do a "clone" operation; instead, you will have to init an empty repository, run the aforementioned config command, add the remote, fetch, then checkout the branch you need with `git checkout -b`. You could create a git alias to simply this procedure, for example (powershell):

```powershell
git config --global alias.clone-https "!f() { newdir=`"`$(basename `"`$1`")`" ; mkdir `"`$newdir`" ; cd `"`$newdir`" ; git init ; git config protocol.https.allow always ; git remote add origin `"`$1`" ; git fetch ; git checkout -b master origin/master ; }; f"
# then you can run:
git clone-https https://git.heroku.com/your-heroku-repo.git
```

## Troubleshooting tips

- verify that git is also loading the Github config file, by running `git config -l --show-origin` in a Github working copy
- if you get the warning "@ WARNING: UNPROTECTED PRIVATE KEY FILE! @" when trying to use the keys, fix the private key(s) file(s) permissions to allow FULL CONTROL to the current user, `SYSTEM` and the `Administrators` group
- if a repository was already cloned via HTTPS and you want to start working via SSH, change its remote via: `git remote set-url origin git@github.com:violinminds/knowledgebass.git`. To verify the current remotes, run: `git remote -v`
- if you get an error, eg. "Permission denied (publickey). fatal: Could not read from remote repository.", to debug, you can add the `-v` switch to `core.sshCommand` setting in the git config to be more verbose (use `-vvv` to be even more verbose)
  - a possible problem might be that, for some reason, ssh is not using the agent's keys. In the verbose debug log, ssh reports a list of the keys it tries to use when connecting to an ssh host, which should also include the agent's keys. If the agent keys are missing, see the following Troubleshooting tips. Example of correct debug log:
  ```ini
    debug1: Will attempt key: git-auth RSA SHA256:yyyyyyyyyyyyyyyyyyyyyyyyyyyy agent
    debug1: Will attempt key: git-signing RSA SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxx agent
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_dsa
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_ecdsa
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_ecdsa_sk
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_ed25519
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_ed25519_sk
    debug1: Will attempt key: C:\\Users\\username/.ssh/id_xmss
  ```
- if by debugging with a verbose `core.sshCommand` or simply by trying the authentication with `ssh -vT git@github.com` you see that ssh is not trying the agent's keys, it might be that the agent service is not running. Start it via `sc start ssh-agent` and try again
  - If the agent is running but the problem is still present, it might be that git is not using the system's OpenSSH suite. This might happen if you didn't select "Use external OpenSSH" during git setup. To verify this, you can either set `core.sshCommand` to `where.exe ssh > ~/sshlocation.txt && notepad ~/sshlocation.txt && rm ~/sshlocation.txt` then run a git command again (eg. git clone), or you can to run `which ssh` in git Bash; both are ways to determine which ssh binary git is trying to run. If it's not the OpenSSH binary (located in C:/WINDOWS/System32/OpenSSH), fix your `PATH` environment variable. If git is using its internal ssh (C:\Program Files\Git\usr\bin), reinstall git for windows and select "Use external OpenSSH"

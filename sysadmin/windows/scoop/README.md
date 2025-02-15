# Scoop

## Fix `scoop update` or any other scoop command errors caused by global git config not allowing `https` as transport
If you have something like this in your `~/.gitconfig`, for any reason:
```gitconfig
[protocol "http"]
	allow = never
[protocol "https"]
	allow = never
[protocol "git"]
	allow = never
[protocol "file"]
	allow = never
[protocol "ssh"]
	allow = always
```

This will stop scoop from working and present a message such as:
```shell
> scoop update
Updating Scoop...
fatal: transport 'https' not allowed
Update failed.
```

To fix it, add to your `~/.gitconfig` an exception for the scoop folder:
```gitconfig
[includeIf "gitdir:C:/Users/*/scoop/**"]
  path = ~/scoop.gitconfig
```
> ⚠️ NB: `gitdir` must end with a trailing slash (`/`).
> 
> ⚠️ NB: `gitdir` is case sensitive, even on Windows.
>
> ⚠️ NB: `gitdir` can contain `*` to allow the use of scoop for every user on the machine.
>
> ⚠️ NB: `gitdir` ends with `**` to allow every scoop command, like `scoop bucket add extras`, which wouldn't work without the `**`

Then create/edit your `scoop.gitconfig` file like this:
```gitconfig
[protocol "https"]
  allow = always
```

Then finally run `git init` in your scoop folder!
```shell
> cd ~
> cd scoop

Now the error disappears!
```shell
> scoop update
Updating Scoop...
Updating Buckets...
Scoop was updated successfully!
```
> git init
```

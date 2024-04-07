# SVN

## Snippets

### Diff only properties of a file

```powershell
svn diff -r 299:300 --depth empty file_or_folder # --depth empty = prop only
```

### Show working copy edits

```shell
svn status -u wc
```

### Delete last revision

This can be used, for example, to fix a broken repo.

In the example below, `r385` of repo `my-repo` will be used.

> - https://superuser.com/a/315138
> - https://svnbook.red-bean.com/en/1.7/svn.ref.svnadmin.html
> - https://www.saas-secure.com/svn-hosting/svn-dump-restore.html

#### 1. create new repo

```shell
cd c:\path\to\svn\repositories # NOT working copies but the repositories folder
svnadmin create my-repo-TMP
```

#### 2. dump old repo INTO new repo

```shell
svnadmin dump -r 0:384 my-repo | svnadmin load my-repo-TMP
```

OR, if it hangs, try this instead

```shell
svnrdump dump http://your-repo/url | svnrdump load http://your-repo-TMP/url
```

If also this errors out, you might have to create an empty pre-revision repository hook (or with "exit 0" content):

- <https://stackoverflow.com/a/44307179>
- <https://stackoverflow.com/a/44307179>

OR, as a last try, dump to a file (`> x.dump`) THEN load it separately; [pay attention to `powershell` fking up line endings](https://stackoverflow.com/questions/48371447/piping-text-to-an-external-program-appends-a-trailing-newline), both in export and import; use `cmd` instead.

#### 3. (maybe not necessary): set new repo UUID equal to old repo

```shell
svnadmin setuuid my-repo-TMP $(svnlook uuid my-repo)
```

OR run `svnadmin info my-repo` and take note of uuid, then run `svnadmin setuuid my-repo-TMP <repo-UUID-here>`.

#### 4. rename/delete repos

```shell
mv my-repo my-repo-OLD # or just RMove it
mv my-repo-TMP my-repo
```

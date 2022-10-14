# Git related commands

### Delete all tags except one
Delete all tags except "2.2.1":
```
git push origin -d $(git tag -l | grep -v '2.2.1') # delete remotely
git tag -d $(git tag | grep -v '2.2.1') # delete locally
```



### Delete all tags with prefix
> https://gist.github.com/shsteimer/7257245?permalink_comment_id=2623569#gistcomment-2623569

Delete all tags with prefix "1.0":
```
git push origin -d $(git tag -l "v1.0*") # delete remotely
git tag -d $(git tag -l "v1.0*") # delete locally
```

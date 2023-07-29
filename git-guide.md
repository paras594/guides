

[toc]

### Initialize Git

```bash
git init
```



### Add files to staging area

```bash
git add . 

# alias
gaa
```



### Remove file from staging area

````bash
git remove --staged <filename>
````



### Check git status

```bash
git status

# alias
gst
```



### Commit changes

```bash
git commit -m "my git message"

# alias
gc -m "my git message"
```


### Uncommit Prev Changes

```shell
git reset --soft HEAD~1
```


 #  ## Check git log

```bash
git log --oneline

# alias
glo
```



### Add file to last commit (Quick Amend)

```bash
git commit --amend --reuse-message HEAD
```



### Create Branch

```bash
git branch mybranch

# alias
gb mybranch
```



### See all branch

```bash
git branch

# alias
gb
```



### Delete Branch

```bash
git branch -d branch-name
```




### Switch to a branch

```bash
git checkout branch_name

#alias
gco branch_name
```



### Merge Branch

````bash
git merge branch_name
````




---
layout: page
title: Git
permalink: /diary/git
---

### Reset your local repo to the remote and overwrite changes

```Git
git fetch origin
git reset --hard origin/master
```

### The command to list all branches in local and remote repositories is:
```git
it branch -a
```
### If you require only listing the remote branches from Git Bash then use this command:
```git
git branch -r
```

### You may also use the show-branch command for seeing the branches and their commits as follows:
```git
git show-branch
```
### Se changes at last commit
```git
git whatchanged <option>…​
```

### Clean repository for sensitive data
Download [BGF](https://rtyley.github.io/bfg-repo-cleaner/) from the its homepage or install it (and Homebrew)
First clone a fresh copy of your repo, using the — mirror flag:  
```git
$ git clone --mirror git://example.com/some-big-repo.git
```
Building replacements.txt file  
```txt
PASSWORD1 #Replace string 'PASSWORD1' with '***REMOVED***' (default)
PASSWORD2==>examplePass         # replace with 'examplePass' instead
PASSWORD3==>                    # replace with the empty string
```
Run the BFG programe
```Java
java -jar bfg --replace replacement.txt
```
Push the repository up again
```Git
$ git reflog expire --expire=now --all && git gc --prune=now --  aggressive
$ git push
```
### Make a new branch
```Git
Git checkout -b “newer-branch”
```
and when you want to push it back
```Git
git push --set-upstream origin “newer-branch”
```
### Deleting local branches in Git
```bash
git branch -d branchToDelete
```
### Deleting remote branches in Git
```bash
git push origin --delete branchToDelete
```
### Deleting both a local and a remote branch
Just a side note: please keep in mind that local and remote branches actually have nothing to do with each other. They are completely separate objects in Git.

Even if you've established a tracking [connection](https://www.git-tower.com/learn/git/faq/track-remote-upstream-branch) (which you should for most scenarios), this still does not mean that deleting one would delete the other, too!

If you want any branch item to be deleted, you need to delete it explicitly.

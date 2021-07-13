---
layout: page
title: Git
permalink: /diary/git
---

## Reset your local repo to the remote and overwrite changes

```Git
git fetch origin
git reset --hard origin/master
```

## The command to list all branches in local and remote repositories is:
```git
git branch -a
```
## If you require only listing the remote branches from Git Bash then use this command:
```git
git branch -r
```

## You may also use the show-branch command for seeing the branches and their commits as follows:
```git
git show-branch
```
## Se changes at last commit
```git
git whatchanged <option>…​
```

## Clean repository for sensitive data
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
## Make a new branch
```Git
Git checkout -b “newer-branch”
```
and when you want to push it back
```Git
git push --set-upstream origin “newer-branch”
```
## Deleting local branches in Git
```bash
git branch -d branchToDelete
```
## Deleting remote branches in Git
```bash
git push origin --delete branchToDelete
```
## Deleting both a local and a remote branch
Just a side note: please keep in mind that local and remote branches actually have nothing to do with each other. They are completely separate objects in Git.

Even if you've established a tracking [connection](https://www.git-tower.com/learn/git/faq/track-remote-upstream-branch) (which you should for most scenarios), this still does not mean that deleting one would delete the other, too!

If you want any branch item to be deleted, you need to delete it explicitly.

## After git 2.16 git outputs to VIM instead of the console
According to [this link](https://github.com/git/git/blob/master/Documentation/RelNotes/2.16.0.txt#L85-L88).
Get back to normal like this:
```bash
git config --global pager.branch false # global setting
git --no-pager <your command> # for each command
```

## Links
1. [Git's home page](https://git-scm.com/) where you can download Git and learn about it as well.
1. Exelent [book](https://www.git-tower.com/learn/git/ebook) from [Tower](https://www.git-tower.com) about learning GIT and there are some videos also

## Renaming branches
If you want to rename a branch while pointed to any branch, do:
```bash
git branch -m <oldname> <newname>
```
If you want to rename the current branch, you can do:
```bash
git branch -m <newname>
```
If you want to push the local branch and reset the upstream branch:
```bash
git push origin -u <newname>
```
And finally if you want to Delete the remote branch:
```bash
git push origin --delete <oldname>
```
A way to remember this is -m is for "move" (or mv), which is how you rename files. Adding an alias could also help. To do so, run the following:
```bash
git config --global alias.rename 'branch -m'
```

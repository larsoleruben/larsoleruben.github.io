---
layout: page
title: Git
permalink: /diary/git
---

## Handy aliases
Make an alias for add and commit (and make the message in vim)
```bash
git config --global alias.ac '!git add -A && git commit'
```

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
If you want to rename a branch while pointing to any branch, do:
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
Or delete local branch
```bash
git branch -d <branch>
```
And finally if you want to Delete the remote branch:
```bash
git push origin --delete <oldname>
```
A way to remember this is -m is for "move" (or mv), which is also how you rename files. Adding an alias could also help. To do so, run the following:
```bash
git config --global alias.rename 'branch -m'
```
## Reset local branch to be exact copy of remote
```bash
git fetch origin
git reset --hard origin/branch-name
```

## What is the meaning of GIT info signs
|   Symbol   	|   meaning
|---	|---
|   *	|   unstaged
|   +	|   staged
|   $	|   stash
|   %	|   untracked files
|   <	|   behind
|   >	|   ahead
|   <>	|   diverged
|   =	|   no difference
|   \|	|   operation in progress
|   ?	|   sparse checkout

## Git with oh-my-zsh
Very informative web page can be found [here](https://kapeli.com/cheat_sheets/Oh-My-Zsh_Git.docset/Contents/Resources/Documents/index)

## Having more github accounts on your working station

If you are using both a company account and also would like to use you personal account, you can have both accounts by following these simple steps or by following the instructions [here](https://www.linkedin.com/pulse/multiple-github-accounts-same-mac-hesham-osama/)
This is a short version
Make a private/public key using ssh-keygen (feel freee to use any name)
```shell
ssh-keygen -t rsa -C "email@personal.com" -f "github-personal"

ssh-keygen -t rsa -C "email@company.com" -f "github-company"
```
Add the keys to the Agent
```shell
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/github-personal
ssh-add --apple-use-keychain ~/.ssh/github-company
```
Add the public keys to your github accounts (PS one at a time)
```shell
pbcopy < ~/.ssh/github-personal.pub

pbcopy < ~/.ssh/github-company.pub
```
Next, go to your GitHub account settings, navigate to "SSH and GPG keys," click "New SSH Key," add a title , paste the public key content into the "Key" field, and save. Repeat this step for all your GitHub accounts.

Configure SSH Alias
```shell
open ~/.ssh/config
# or if it does not exists 
touch ~/.ssh/config
```
Add the aliases to the config file
```shell
Host github.com-personal
   AddKeysToAgent yes
   AddKeysToAgent yes
   HostName github.com
   User git
   IdentityFile ~/.ssh/github-personal

Host github.com-company
   AddKeysToAgent yes
   AddKeysToAgent yes
   HostName github.com
   User git
   IdentityFile ~/.ssh/github-company
```

Clone and Configure Repositories
```shell
git clone git@github.com-company:github-company/{repo-name}.git
git clone git@github.com-personal:github-personal/{repo-name}.git
```
**If you have "old repo's" you can get assign them to one of your accounts by going into the .git folder and open the config file and change the url to either git@github.com-company/..... or git@github.com-personal/.....
Then you don't have to "re-clone" the repo.**

Thanks to [Hesham Osama](https://www.linkedin.com/pulse/multiple-github-accounts-same-mac-hesham-osama/#:~:text=Published%20by-,Hesham%20Osama,-Senior%20Software%20Engineer)

## Most Useful Git Commands from chat gpt

1. **`git init`**
   - **Usage**: Initializes a new Git repository and starts tracking an existing directory.
   - **Explanation**: Adds a hidden subfolder for version control.

2. **`git clone [URL]`**
   - **Usage**: Clones a repository from an existing URL.
   - **Explanation**: Copies a remote repository to your local machine.

3. **`git status`**
   - **Usage**: Shows the status of changes.
   - **Explanation**: View changes in your working directory.

4. **`git add [file/directory]`**
   - **Usage**: Adds changes to the staging area.
   - **Explanation**: Prepares changes for commit.

5. **`git commit -m "[Descriptive message]"`**
   - **Usage**: Captures a snapshot of the project's changes.
   - **Explanation**: Saves your changes to the repository.

6. **`git log`**
   - **Usage**: Shows the commit logs.
   - **Explanation**: View your commit history.

7. **`git diff`**
   - **Usage**: Shows unstaged file differences.
   - **Explanation**: See changes since last commit.

8. **`git diff --staged`**
   - **Usage**: Shows staged differences.
   - **Explanation**: View changes you've staged.

9. **`git reset [file]`**
   - **Usage**: Unstages a file.
   - **Explanation**: Remove a file from the staging area.

10. **`git checkout [branch-name]`**
   - **Usage**: Switches branches.
   - **Explanation**: Move between different branches.

11. **`git branch`**
   - **Usage**: Lists all branches.
   - **Explanation**: View all local branches.

12. **`git branch -a`**
   - **Usage**: Lists all branches, local and remote.
   - **Explanation**: View all branches.

13. **`git branch [branch-name]`**
   - **Usage**: Creates a new branch.
   - **Explanation**: Start a new line of development.

14. **`git push [remote-name] [branch-name]`**
   - **Usage**: Pushes changes to remote.
   - **Explanation**: Updates the remote with your commits.

15. **`git pull [remote-name] [branch-name]`**
   - **Usage**: Fetches and merges remote changes.
   - **Explanation**: Updates local codebase.

16. **`git merge [branch-name]`**
   - **Usage**: Merges another branch into the current branch.
   - **Explanation**: Combine branch histories.

17. **`git remote`**
   - **Usage**: Lists remote repositories.
   - **Explanation**: View linked remote repositories.

18. **`git remote add [name] [URL]`**
   - **Usage**: Adds a remote repository.
   - **Explanation**: Link to another repository.

19. **`git fetch`**
   - **Usage**: Fetches changes from remote.
   - **Explanation**: Get changes without merging.

20. **`git rm [file]`**
   - **Usage**: Deletes a file and stages the change.
   - **Explanation**: Remove a file from your repository.

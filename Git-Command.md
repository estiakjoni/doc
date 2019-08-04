## What is Git?

Git is a distributed version-control system for tracking changes in source code during software development.

## Concepts Of Git

- Keeps tarack of code history
- Takes "snapshots" of your file
- You decide when to take a snapshots by making a "commit"
- You can visit any snapshot at any time
- You can stage files before commit


## Basic Commands
```git init``` 					Initailze local Git repository

```git add <file>``` 			Add/Track file(s) to index

```git add *.html``` 			Add/Track only html extension file(s) to index

```git add .``` 				Add/Track all type of file(s) to index

```git rm --cached <file>``` 	Remove/Untracked file(s) from index

```git rm -r -f --cached <directory>``` Force remove/untracked directory from index

```git status``` 				Check status of working text

```git config --global user.name 'My Name'``` Add name

```git config --global user.email 'email@example.com'``` Add Email

```git commit``` 				Commit changes in index

```git commit -m '<msg>'``` Commit changes in index with message

```git push``` 					Push to remote respository

```git pull``` 					Pull latest from remote respository

```git clone``` 				Clone respository into a new directory

```git log``` 				To see commited logs

## Installing Git

- Linux (Debian)
```sh
sudo apt-get install git
```

- Linux (Fedora)
```sh
sudo yum install git
```

- Mac

```<link>``` : <https://git-scm.com/download/mac>

- Windows

```<link>``` : <https://git-scm.com/download/win>

## Git Ignore
A gitignore file specifies intentionally untracked files that Git should ignore. Patterns read from a .gitignore file in the same directory as the path. Each line in a gitignore file specifies a pattern.

**Create .gitignore file** `touch .gitignore`

**.gitignore file writing pattern format**

`*.html` ignore all html files

`log.txt` ignore 'log.txt' file

`/mydir` ignore 'mydir' directory

'.idea/*' 

## Generating a new SSH key and adding it to the ssh-agent
**Generating a new SSH key**
- Open Git Bash.
- Paste the text below, substituting in your GitHub email address.

`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

- When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location (C:/Users/you/.ssh/id_rsa)

**Adding your SSH key to the ssh-agent**

Before adding a new SSH key to the ssh-agent to manage your keys, you should have checked for existing SSH keys and generated a new SSH key.

If you have GitHub Desktop installed, you can use it to clone repositories and not deal with SSH keys. It also comes with the Git Bash tool, which is the preferred way of running git commands on Windows.

- Ensure the ssh-agent is running
```
eval $(ssh-agent -s)
```
> Agent pid 59566 //result

- Add your SSH private key to the ssh-agent. If you created your key with a different name, or if you are adding an existing key that has a different name, replace id_rsa in the command with the name of your private key file.

```
ssh-add ~/.ssh/id_rsa
```

## Create a new repository on the command line

```sh
git init
echo ".gitignore" >> .gitignore
git add .
git commit -m "first commit"
git remote add origin git@github.com:GitUsername/repositoryname.git
git push -u origin master
```

## Git branch
Git, branches are a part of your everyday development process. Git branches are effectively a pointer to a snapshot of your changes.

**Create a branch**
``` git branch <name> ```

**Switch branch**
``` git checkout <name> ```

**Merge file(s) with master**
``` git merge <BranchName> -m '<msg>' ```


## Change Git Commit Message, Even After Push

<pre>
git log

git commit --amend

git rebase -i HEAD~2

git push -f

git log --oneline
</pre>


## Git Reset and Clean (Restore to last commit)

```bash
git reset --hard;git clean -df;
```

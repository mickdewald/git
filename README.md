# Git
https://git-scm.com/doc
http://gitless.com/
https://githowto.com/setup
https://www.gerritcodereview.com/
http://gource.io/




&nbsp;
# TL;DR
## Diff
```bash
# Exclude files and folders from diff
git diff -- . ':(exclude)yarn.lock' ':(exclude)assets/'
```

## Add
```bash
# Exclude files and folders from add
git add -- . ':(exclude)config/app.conf' ':(exclude)config/aws-rds-combined-ca-bundle.pem'
```

## Commit
```bash
# Commit with message
git commit -m "My commit message"
```

## Push
```bash
# Push existing branch to the server
git push

# Push new local branch branch to the server
git push --set-upstream origin cra-to-vite
```




&nbsp;
# Hosting
https://gogs.io/
https://gitlab.com/
https://github.com/




&nbsp;
# Windows
```bash
D:\Users\<User>\AppData\Local\Programs\Git\cmd\git.exe
```




&nbsp;
# Why git?
Distributed version control system, each user owns a complete version of the repository, thus also #backups according to #users ➝ Server failure is easy to get over.

Most operations possible without internet connection comparisons between different file and repository versions the creation of branches as well as the local storage of commits

Best practice is to work with stable, main and feature branches

<img src="assets/git-branches-1.png" width="400">
<img src="assets/git-lifecycle.png" width="500">




&nbsp;
# Global Setup
```bash
git config --global user.email "me@domain.com"
git config --global user.name "Firstname Lastname"
```

Will be witten into `~/.gitconfig`
```bash
# ~/.gitconfig
[user]
	email = me@domain.com
	name = Firstname Lastname
```




&nbsp;
# .gitignore
see example file

Clear cache in case of ignored files are still tracked
```bash
# Show untracked files or changes
git status

# Important, commit pending files first!
git commit -am "commit pending files"

# Delete files, folders. --cached prevents delete from file system. -rf (recursive) deletes subfolders. -f (force) ignores changes to files.
git rm -rf --cached folderToRemove

git add .
git commit -am "fixed untracked files"
```




&nbsp;
# Common Tasks
## Checkout a new repository
```bash
# Checkout
git clone git@gitlab.com:username/repository.git

# enter the directory to push to the remote repository
cd repository

# Create README.md
touch README.md

# Add README.md to staging area
git add README.md

# commit to local repository
git commit -m "add README"

# push to remote repository
git push -u origin main
```


## Create new repository from existing folder
```bash
# enter the directory to push to the remote repository
cd existing_folder

# mark as a git repository and create initial git meta files
git init

# tell the local repository to wire with the remote repository
git remote add origin git@gitlab.com:username/repository.git

# add all local files to staging area
git add .

# commit to local repository
git push -u origin main

# push to remote repository
git commit
```


## Add an existing Git repository to a new remote git repository
```bash
# enter the directory to push to the remote repository
cd existing_repo

# tell the local repository to wire with the remote repository
git remote add origin git@gitlab.com:username/python.git

git push -u origin --all
git push -u origin --tags
```


## Undo last commit
http://stackoverflow.com/questions/927358/how-to-undo-last-commits-in-git
```bash
git commit -m "Something terribly misguided"             	# (1)
git reset HEAD~                                          	# (2) git reset --hard HEAD~1 (--hard = your files are reset to their state at commit B, reset HEAD~1 = move the HEAD pointer back one commit)

# edit files as necessary                                   # (3)
git add ...                                               	# (4)
git commit -c ORIG_HEAD                                   	# (5)
```

1. This is what you want to undo

2. This leaves your working tree (the state of your files on disk) unchanged but undoes the commit and leaves the changes you committed unstaged (so they'll appear as "Changes not staged for commit" in git status and you'll need to add them again before committing). If you only want to add more changes to the previous commit, or change the commit message1, you could use git reset --soft HEAD~ instead, which is like git reset HEAD~ but leaves your existing changes staged.

3. Make corrections to working tree files.

4. git add anything that you want to include in your new commit.

5. Commit the changes, reusing the old commit message. reset copied the old head to .git/ORIG_HEAD; commit with -c ORIG_HEAD will open an editor, which initially contains the log message from the old commit and allows you to edit it. If you do not need to edit the message, you could use the -C option.



## Rebase vs Merge
https://www.youtube.com/watch?v=Nftif2ynvdA
https://www.atlassian.com/git/articles/git-team-workflows-merge-or-rebase

Rebase as team policy is a different thing than rebase as cleanup.
Rebase as cleanup is a healthy part of the coding lifecycle of the git practitioner.

Let me detail some example scenarios that show when rebasing is reasonable and effective (and when it's not):
* You're developing locally.
You have not shared your work with anyone else. At this point, you should prefer rebasing over merging to keep history tidy. If you've got your personal fork of the repository and that is not shared with other developers, you're safe to rebase even after you've pushed to your fork.

* Your code is ready for review.
You create a pull request, others are reviewing your work and are potentially fetching it into their fork for local review. At this point you should not rebase your work. You should create 'rework' commits and update your feature branch. This helps with traceability in the pull request, and prevents the accidental history breakage.

* Review is done and ready to be integrated into the target branch.
Congratulations! You're about to delete your feature branch. Given that other developers won't be fetch-merging in these changes from this point on, this is your chance to sanitize history. At this point you can rewrite history and fold the original commits and those pesky 'pr rework' and 'merge' commits into a small set of focussed commits. Creating an explicit merge for these commits is optional, but has value. It records when the feature graduated to main.

https://stackoverflow.com/questions/3357122/git-pull-vs-git-fetch-git-rebase
So let's suppose you're in the common case - you've done some work on your main branch, and you pull from origin's, which also has done some work. After the fetch, things look like this:

```bash
- o - o - o - H - A - B - C (main)
               \
                P - Q - R (origin/main)
```

If you merge at this point (the default behavior of git pull), assuming there aren't any conflicts, you end up with this:

```bash
- o - o - o - H - A - B - C - X (main)
               \             /
                P - Q - R --- (origin/main)
```

If on the other hand you did the appropriate rebase, you'd end up with this:

```bash
- o - o - o - H - P - Q - R - A' - B' - C' (main)
                          |
                          (origin/main)
```

The content of your work tree should end up the same in both cases; you've just created a different history leading up to it. The rebase rewrites your history, making it look as if you had committed on top of origin's new main branch (R), instead of where you originally committed (H). You should never use the rebase approach if someone else has already pulled from your main branch.

Finally, note that you can actually set up git pull for a given branch to use rebase instead of merge by setting the config parameter branch.`<name>`.rebase to true. You can also do this for a single pull using git `pull --rebase`.



## Checkout remote branch
```bash
git branch -r	 -v -a                              # remote branches auflisten, list remote branches
git fetch
git checkout <branchname>                           # checkout a new branch locally called <branchname> based on remote origin/<branchname>
```


## Create branch
Create a new branch based on an existing branch in the desired project
```bash
git checkout -b <new-branch>
```

Announce changes in the system
Add and commit files
```bash
git add .
git commit -am "my commit message"
```

Optional: ➝ push branch to remote repository
```bash
# Since Git 1.8.0
git push -u origin <branch>
```

Optional: push all branches to remote repository
```bash
git push --all -u
```


## Delete local branch
```bash
git branch
git branch -d <branch_name>
```


## Delete remote branch
```bash
# merge-request overwrite with "nothing" (the part before the :)
git push origin :merge-request

# alternative:
# Delete Remote Branch, Delete remote branch (see also git branch -d branch_name)
git push origin --delete <branch_name>
```


## Merge branch into main
http://stackoverflow.com/questions/5601931/best-and-safest-way-to-merge-a-git-branch-into-main
```bash
git checkout main
git pull origin main
git merge <branch_to_merge_in_main>
git push origin main
git tag -a v3.1.1
```



## Resolve merge conflict
http://meldmerge.org/help/resolving-conflicts.html
http://stackoverflow.com/questions/11133290/git-merging-using-meld
http://stackoverflow.com/questions/34119866/setting-up-and-using-meld-as-your-git-difftool-and-mergetool/34119867#34119867

Add Meld.exe to Path
```bash
...%GRADLE_HOME%\bin;...;C:\Program Files (x86)\Meld\;
```

Update gitconfig
```bash
# ~/.gitconfig in Windows
[user]
	email = user@domain.com
	name = username

[diff]
   tool = meld

[difftool "meld"]
   prompt = false
   cmd = meld.exe $LOCAL $REMOTE

[merge]
   tool = meld

[mergetool "meld"]
   prompt = false
   keepBackup = false
   keepTemporaries = false
   cmd = meld.exe $BASE $LOCAL $REMOTE $MERGED
   #cmd = meld "$LOCAL" "$BASE" "$REMOTE" --output "$MERGED"
   # Don't forget to enter the proxy also in .bashrc or .bash_profile

[http]
	proxy = “http://proxyurl\\username:pass@proxyurl:8080”

[https]
	proxy = “http://proxyurl\\username:pass@proxyurl:8080”
```

Testen
```bash
git difftool <COMMIT_HASH> file_name
git difftool <BRANCH_NAME> file_name
git difftool <COMMIT_HASH_1> <COMMIT_HASH_2> file_name
```


## Compare branches (local branch with remote branch)
```bash
git fetch
git diff main origin/main
git diff branch_1..branch_to_compare
git diff branch_1...branch_to_compare
git diff --name-status branch..branch_to_compare
```


## Your local changes to the following files would be overwritten by merge
```bash
git fetch
git reset origin/main --hard
```


## What is "origin" in Git?
origin is an alias on your system for a particular remote repository. It's not actually a property of that repository. By doing
```bash
git push origin branchname
```
you're saying to push to the origin repository. There's no requirement to name the remote repository origin: in fact the same repository could have a different alias for another developer.

Remotes are simply an alias that store the url of repositories. You can see what url belongs to each remote by using
```bash
git remote -v
```
In the push command, you can use remotes or you can simply use a url directly. An example that uses the url
```bash
git push git@github.com:git/git.git main
git push remote main
```


## Replace branch with main
```bash
git checkout dev
git reset --hard main
```

## ^M Endings, Line Endings, Windows CRLF, Unix LF, Mac loved CR
https://stackoverflow.com/questions/1822849/what-are-these-ms-that-keep-showing-up-in-my-files-in-emacs
https://stackoverflow.com/questions/9225599/ should-git-change-the-ending-of-line-controlm-or-m-at-the-end-of-the-first-l

Error message:
```bash
warning: LF will be replaced by CRLF in ...
The file will have its original line endings in your working directory
```

On Windows
https://stackoverflow.com/questions/1889559/git-diff-to-ignore-m

This basically tells Git that an end-of-line CR is not an error. As a result, those annoying ^M characters no longer appear at the end of lines in git diff, git show
```bash
# Works
git config --global core.safecrlf false

# To test
git config --global core.whitespace cr-at-eol

# Does not work
#git config --global core.autocrlf true
```

Mac/Linux, use
```bash
git config --global core.autocrlf input
```

This command enters the .gitconfig the following
```bash
# ~/.gitconfig
[core]
	autocrlf = true
```


## Add all files to a commit except a single file?
```bash
git add -u
git reset -- main/dont_check_me_in.txt
```

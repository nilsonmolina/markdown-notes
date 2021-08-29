
# Using Multiple GitHub accounts on single Machine
**Read the note at the end of this section before starting**  
I needed this in order to work on personal git repos on my work machine. These are the 4 steps required:
1. Generate SSH key for the new account
1. Attach this new key to your GitHub account
1. Create an SSH config file
1. Set a new remote url for repos

### Generate SSH Key
Generate an SSH key with the following command. Be sure to replace with your own email address
```bash
$ cd ~/.ssh
$ ssh-keygen -t rsa -C '<your-email>@gmail.com'
```
 You will then be asked for a file name and a password. Name it what you like and press enter twice to leave the password empty (feel free to use a password if you prefer). Below is some example output:
 ```bash
$ ssh-keygen -t rsa -C 'molinanilson@gmail.com'

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/nmolina/.ssh/id_rsa): testing

Enter passphrase (empty for no passphrase):
Enter same passphrase again:

Your identification has been saved in testing.
Your public key has been saved in testing.pub.
The key fingerprint is:
SHA256:NcfQIkhY9bV15BfGyaliw0Gxf2fSO9wOY7GNlICcr4k molinanilson@gmail.com
The key's randomart image is:
+---[RSA 3072]----+
|     +oo. o+o ++=|
|    . . .o.Boo.B.|
|         .Oo* . o|
|         . O.o o.|
|        S . +.=.+|
|         . o .oB+|
|        E o   *+o|
|             . +.|
|                .|
+----[SHA256]-----+
```

### Attach this key to your GitHub Account
1. Copy the public key w/ the following command:
    ```bash
    $ pbcopy < ~/.ssh/testing.pub
    ```
1. Sign in to your personal GitHub account
1. Click on `Settings`
1. Select `SSH and GPG keys` from the menu on the left
1. Click on `New SSH Key` button and paste the key in
1. Click `Add Key` - and you're done!
---

### Create an SSH config File
Now we can set SSH configuration rules for different hosts. Feel free to change the `HOST`  and `IdentityFile` props to fit your needs.

```bash
$ cd ~/.ssh
$ vim config


# Work Github account - default config
Host github.com
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa

# Personal Github account -
Host github.com-personal
        HostName github.com
        User git
        IdentityFile ~/.ssh/github_personal

Host *
    UseKeychain yes
```

### Set a new remote url
Any git repos that you clone down from your personal github will require a new remote url in order to properly authenticate.
```
$ git clone git@github.com:nilsonmolina/markdown-notes.git

$ git remote -v
origin	git@github.com:nilsonmolina/markdown-notes.git (fetch)
origin	git@github.com:nilsonmolina/markdown-notes.git (push)
```

Change the remote url to match the `HOST` prop you put in the SSH config file.
```bash
$ git remote set-url origin git@github.com-personal:nilsonmolina/markdown-notes.git
```

And thats it! Now you can push up any changes to this repo without any issues!

**Reference**  
https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca/

## Notes
**These steps will still use your work email/account.**

Annoying since it shows that my work account is commiting to my personal projects. So to fix that, I'm switching to a different github user by simply changing my email:
```bash
# Check current git user
$ git config user.email

# Change to different git user
$ git config user.emal 'molinanilson@gmail.com'

# Not always necessary, but can also use the global flag
$ git config --global user.email 'molinanilson@gmail.com'\
```



---


# Changing Your Git Author Identity
There are three ways to change your committer identity in Git. All of these methods only affect future commits, not past ones!
### Changing Your Committer Name & Email Globally
You can run the "git config" command with the --global flag; this will make sure all of your future commits use the given information:
```bash
$ git config --global user.name "John Doe"
$ git config --global user.email "john@doe.org"
```
### Changing Your Committer Name & Email per Repository
If you want to use special settings only when working in a certain repository, you can simply omit the --global flag. This makes the configuration valid only in that repository:
```bash
$ git config user.name "John Doe"
$ git config user.email "john@doe.org"
```
### Changing the Author Information Just for the Next Commit
Finally, with the --author flag, you can also overwrite the author information for just the next commit:
```bash
$ git commit --author="John Doe <john@doe.org>"
```

Reference:  
https://www.git-tower.com/learn/git/faq/change-author-name-email
  

---

  
# Editing the Author of Past Commits
If we do this, we are effectively rewriting commit history. This is nothing to take lightly: you will create new commit objects in this process, which can become a serious problem for your collaborators - because they might have already based new work on some of the original commits. Therefore, think twice before you rewrite your commit history!

---

### Using `--amend` for the Very Last Commit
In case you want to change just the very last commit, Git offers a very easy way to do this:
```bash
$ git commit --amend --author="John Doe <john@doe.org>"
```
This effectively replaces the last commit with your "edited" version, correcting the wrong author information.

---

### Using Interactive Rebase
Interactive Rebase is the Swiss Army Knife of tools in Git: it allows you to do and change almost anything. Use it with care!  


The first step is to identify the last "good" commit and provide its hash to the rebase command or use `--root` for all commits:
```
$ git rebase -i -p --root
```
Your editor will open, requesting you to mark all the commits you want to change with the "edit" keyword. For every commit that you want to edit, change 'pick' to 'edit', then save and close (`:wq` if using vim).

Git will now walk you through each commit, giving you the chance to mold it as you desire:
```bash
Stopped at 5772b4bf2... Add images to about page
You can amend the commit now, with

    git commit --amend

Once you are satisfied with your changes, run

    git rebase --continue
```
Your job, now, is to correct the author information and then continue to the next concerned commit object until you've edited all the commits you just marked:
```bash
$ git commit --amend --author="Nilson Molina <nmolina@student.42.us.org>" --no-edit
$ git rebase --continue
```

---

### Using `git filter-branch`
Another way is to use Git's "filter-branch" command. It allows you to batch-process a (potentially large) number of commits with a script.  

You can run the below sample script in your repository (filling in real values for the old and new email and name):
```bash
$ git filter-branch --env-filter '
WRONG_EMAIL="wrong@example.com"
NEW_NAME="New Name Value"
NEW_EMAIL="correct@example.com"

if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```
The same warning applies to this method as to the others mentioned - you are rewriting history with this command, creating new commit objects along the way!

Preferably, you should only do this in repositories that haven't been published / shared, yet. In any other case you should use it with extreme care - and only if you're aware of the side effects!

Reference:  
https://www.git-tower.com/learn/git/faq/change-author-name-email


---

# Merge Repositories
Prep by making Repository B into a directory:
```bash
$ cd PATH/TO/B
$ mkdir FOLDER_NAME
$ mv $(ls | grep -v FOLDER_NAME) FOLDER_NAME
$ git remote add origin url_to_rA
```

Change the remote origin of B to that of A:
```
$ git remote rm origin
$ git remote add origin URL_TO_REMOTE-A
```

Rename the local master branch of B:
```
$ git checkout master
$ git branch -m master-holder
```

Pull all the code of A from remote-A into your local B repo:
```
$ git fetch
$ git checkout master
$ git pull origin master
```

Merge *master-holder* into *master*:
```
$ git merge master-holder --allow-unrelated-histories
```

> Running `git log` should show all the commits from A, the merge commit, and finally all the commits from B.

Push everything to remote-A
```
$ git push origin master
```

Reference:  
https://gist.github.com/msrose/2feacb303035d11d2d05


---


# Removing a File from Git Commit History
### Removing a file from all history
If you commit sensitive data, such as a password or SSH key into a Git repository, you can remove it from the history.

1. If you don't already have a local copy of your repository with sensitive data in its history, clone the repository to your local computer.
    ```
    $ git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY
    ```

2. Navigate into the repository's working directory.
    ```
    $ cd YOUR-REPOSITORY
    ```

3. Run the following command, replacing PATH-TO-YOUR-FILE with the path to the file you want to remove, not just its filename. These arguments will:
    - Force Git to process, but not check out, the entire history of every branch and tag
    - Remove the specified file, as well as any empty commits generated as a result
    - Overwrite your existing tags
    ```bash
    $ git filter-branch --force --index-filter \
    'git rm --cached --ignore-unmatch PATH-TO-YOUR-FILE' \ --prune-empty --tag-name-filter cat -- --all
    ```

4. Once you're happy with the state of your repository, force-push your local changes to overwrite your GitHub repository, as well as all the branches you've pushed up:
    ```
    git push origin --force --all
    ```
5. In order to remove the sensitive file from your tagged releases, you'll also need to force-push against your Git tags:
    ```
    git push origin --force --tags
    ``` 

Reference:  
https://help.github.com/articles/removing-sensitive-data-from-a-repository/


---

### Removing a file added in the most recent unpushed commit
To remove a large file from your repository, you must completely remove it from your local repository and from GitHub.
If the file was added with your most recent commit, and you have not pushed to GitHub, you can delete the file and amend the commit:

1. Open Terminal.

2. Change the current working directory to your local repository.

3. To remove the file, enter `git rm --cached`:
    ```bash
    $ git rm --cached giant_file
    # Stage our giant file for removal, but leave it on disk
    ```

4. Commit this change using --amend -CHEAD:
    ```bash
    $ git commit --amend -CHEAD
    # Amend the previous commit with your change
    # Simply making a new commit won't work, as you need
    # to remove the file from the unpushed history as well
    ```

5. Push your commits to GitHub:
    ```bash
    $ git push
    # Push our rewritten, smaller commit
    ```

Reference:  
https://help.github.com/articles/removing-files-from-a-repository-s-history/
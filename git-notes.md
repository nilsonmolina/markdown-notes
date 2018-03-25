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
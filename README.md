# Create a new project based on the QuickStart

Clone this repo into new project folder (e.g., `my-proj`).
```bash
git clone  https://github.com/angular/quickstart  my-proj
cd my-proj
```

We have no intention of updating the source on `angular/quickstart`.
Discard everything "git-like" by deleting the `.git` folder.
```bash
rm -rf .git  # non-Windows
rd .git /S/Q # windows
```

## Create a new git repo
You could [start writing code](#start-development) now and throw it all away when you're done.
If you'd rather preserve your work under source control, consider taking the following steps.

Initialize this project as a *local git repo* and make the first commit:
```bash
git init
git add .
git commit -m "Initial commit"
```

Create a *remote repository* for this project on the service of your choice.

Grab its address (e.g. *`https://github.com/<my-org>/my-proj.git`*) and push the *local repo* to the *remote*.
```bash
git remote add origin <repo-address>
git push -u origin master
```
# Install npm packages

Install the npm packages described in the `package.json` and verify that it works:

**Attention Windows Developers:  You must run all of these commands in administrator mode**.

```bash
npm install
npm start
```

> If the `typings` folder doesn't show up after `npm install` please install them manually with:

> `npm run typings -- install`

The `npm start` command first compiles the application, 
then simultaneously re-compiles and runs the `lite-server`.
Both the compiler and the server watch for file changes.

Shut it down manually with Ctrl-C.

You're ready to write your application.

# Branching the repo

Using cmd/terminal, navigate to the project root folder and enter:

```bash
git checkout -b <branch name>
```

shorhand for
    
```bash
git branch <branch name>
git checkout <branch name>
```
    
to push branch, first make sure you've checked out the right branch

```bash
git checkout <branch name>
```
    
push branch to remote repository
    
```bash
git push -u origin <branch name>
```


# Show all local branches of repo

```bash
git branch
```

# Show remote repo name and URL

```bash
git remote -v
```

# Replace all changes in a messed up local branch to the origin/master

```bash
git reset --hard origin/master
```
says: throw away all my staged and unstaged changes, forget everything on my current local branch and make it exactly the same as origin/master

# Merging and removing finished branches

Switch to branch you want to merge into then type:

```bash
git merge <branch name>
```

# Delete Branch

To delete when done merging

```bash
git branch -d <branch name>
```

If no merge has been done, use -D

```bash
git branch -D <branch name>
```
# Create local branch that tracks a remote branch
You need to create a local branch that tracks a remote branch. The following command will create a local branch named `daves_branch`, tracking the remote branch `origin/daves_branch`. When you push your changes the remote branch will be updated.

For most versions of git:

```bash
git checkout --track origin/daves_branch
```

`--track` is shorthand for `git checkout -b [branch] [remotename]/[branch]` where `[remotename]` is origin in this case and `[branch]` is twice the same, `daves_branch` in this case.

For git 1.5.6.5 you needed this:

```bash
git checkout --track -b daves_branch origin/daves_branch
```

For git 1.7.2.3 and higher this is enough (might have started earlier but this is the earliest confirmation I could find quickly):

```bash
git checkout daves_branch
```

Note that with recent git versions, this will command not create a local branch and will put you in a 'detached HEAD' state. If you want a local branch, use the `--track option`. Full details here: [Git Branching Remote Branches - Tracking Branches](http://git-scm.com/book/en/v2/Git-Branching-Remote-Branches#Tracking-Branches)


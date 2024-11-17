Git is Distributed Version Control System. Distributed means that every developer has complete copy of codebase including it's entire history of changes and who changed what.

The default branch in previous git versions was ==master== but nowadays it is ==main==. 

First we need to initialize the repo in our local computer. Then, we need to add to git. after adding. we need to commit the changes to git. committing means taking snapshots of code at the time. we can come to that commit later whenever we want it. It works as checkpoints like in racing game. 
### Commands in git
##### Initialize the git
```
git init
```
##### Add the file to git in local
```
git add .
git add ./ // it add all the new files and changed file
```
##### Commit the files
Committing means taking snapshots of code at the time. we can come to that commit later whenever we want it. It works as checkpoints like in racing game. 
```
git commit -m "some text"
```
##### Logs in git
It provides information about the commits that may be done by different members. It has commitid which is unique to every commit, Author, Date and commit-text.
### Remote Git
Git automatically refers to remote repo by origin by initialization. origin in git just means the remote repository URL. It is an alias for remote repository URL. At first we add the origin to our project URL.
```
git remote add origin "your project url" // here, we are preparing the origin to be alias fo the "your project url"
```
Now it is done, we need to push the code to remote repo. So, we are pushing with origin as your project URL.
```
git push -u origin main // here origin means "your project url"
git push -u "your project url" main

git push -u origin branch-name // if only branch need pushing
```
`-u` also means `--set-upstream`. By using `-u`, we can use simple `git push` and `git pull` commands without specifying the remote and branch name again. It simplifies future commands, as Git will know which remote branch to track i.e. if we specify `-u` , `git push` means we can just push it and git will understand which branch to push or pull. If we don't use `u`, we need to specify the branch i.e. `git push origin branch-name`
### Branching and Merging
Branching means create a branch and different developers can make their own change. Later we can merge. 
![[Pasted image 20241010123346.png]]

##### To make a branch
```
git branch branch-name
```
##### To switch the branch
```
git checkout branch-name
```

> [!NOTE] So, which code does get new branch get
> New branch has code which is based on the branch you are currently in. if you are in `main` branch then, new branch has code of main branch. To avoid this. we can use this command `git branch new-branch source-branch`
> 

#### Stashing
When we change the code from one branch and try to go to other branch. we need stashing. if allows to temporarily save the work that we have done without committing.
```
git stash          # Save changes
git stash apply    # Apply the latest stash
git stash pop      # Apply and remove the stash
```
### Pull
A pull request lets you share your changes with your team. Once approved and merged, your changes goes to `main` branch
```
git pull // it pull code from that branch that you are in from remote branch
```

### Standard Workflow in Git that I might follow
1. Clone the repository from remote repo
2. Create a additional repo with name `feat` or other name based on `main` branch or other desired branch
3. Make the changes.
4. Push the branch to remote repo
5. Open a pull request
6. Merge the changes (usually done by project manager)
7. Pull the merged changes into your local main and repeat the process
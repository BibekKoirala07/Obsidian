Git is Distributed Version Control System. Distributed means that every developer has complete copy of codebase including it's entire history of changes and who changed what.

The default branch in previous git versions was ==master== but nowadays it is ==main==. 

First we need to initialize the repo in our local computer. Then, we need to add to git. after adding. we need to commit the changes to git. committing means taking snapshots of code at the time. we can come to that commit later whenever we want it. It works as checkpoints like in racing game. 
### Commands in git
##### Initialize the git
```
git init
git add .
git add ./ // it add all the new files and changed file
```
##### Commit the files
Committing means taking snapshots of code at the time. we can come to that commit later whenever we want it. It works as checkpoints like in racing game. 
```
git commit -m "some text"
```
##### Logs in git
It provides information about the commits that may be done by different members. It has committed which is unique to every commit, Author, Date and commit-text.
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
2. Create a additional repo with name `feat` or other name based on `main` branch or other desired branch.
3. Make the changes.
4. Push the branch to remote repo
5. Open a pull request
6. Merge the changes (usually done by project manager)
7. Pull the merged changes into your local main and repeat the process


### Centralized VCS and Decentralized VCS
Centralized VSC means like Google Docs and decentralized VCS means like git

### Git Introduction
At its core, git is a key-value store. Like every piece of data(file, commits, hash) are stored as object. Git ==doesn't store differences==. Instead every commit is the snapshot of the project. 

### Little bit Theory
There are four main object type in git. Blob, Commits, Tag and Tree. Each Object is defined by 40 character Hex-string(SHA-1 hash)

Blob just means content of the file. Git doesn't care about filename in Blog. Just what is inside the file. that is it. Let's say I write `Hello world` in `text.txt`. git only stores the file content in Blog. Not the name at all. 

Tree is also there. It represent the directory. 

Commit represent the snapshot of the project at a time. It stores hash of the tree (root directory at the commit). Parent Commit to form history chain and metadata(like author, timestamp, message, committer).

Tag is the label that points to the specific commit. There are two kinds of tag. Light weight Tag and Annotated Tag.

Git also use Ref (like pointer and tags) to keep track of "where we are". It is just human friendly references to commit. 

So In Short,
Blob - what is inside the book
Tree - table of content or map of the project at specific moment. pointer to sub tree or blob. in simple tracks, filename and folder structure. 
Commit - A snapshot of whole library at that time.
Head - a bookmark saying that  you are looking at. 

So, Head point to master branch. master points to Commit A. Commit A point to Tree T(folder snapshot) and Tree T points to Blob B(file content).
```js
HEAD → master → Commit A
                  └── Tree T (hash: T1)
                         └── hello.txt → Blob B ("Hello Git")
```
### Understanding through practical
Let's say we initialize the repo. To understand about git repo, we need to have a look inside `.cat` folder. It is the heart of git. 
We may have things like 
###### Head
Since we haven't committed anything, it points to `refs/head/master`. At first, it doesn't exists. 
###### `config`
It has repo-specific git config(It could be things like remotes, user-info, etc.)
###### `description`
It was used in ancient Git Web UI. Ignore it.
###### `branches/`
It is not used nowadays.
###### `hooks`
It is sample hook scripts(disabled by default). You can rename/remove `.sample` to activate them.
###### `/info`
contain the `exlcude` file(local ignore rule)
###### `objects/`
This is exactly where git stores real data like blob objects, trees and commits. Of course, at first it will be empty. After doing commit, we will see actual files and folders. 
###### `/refs`
contains reference(pointer) to things like branches, commits, trees.
###### `COMMITEDIT_MSG`
As name suggest, it says about last commit message.
###### `index`
it is a binary file which says, what files are staged.

Now as soon as we commit, git stored 3 things. That is blog with file content `Hello world`. Git stores it. like 'stores the bytes of this file'. And also Tree was generated. Represent the folder at the commit time. Says "There is a file called "hello.txt" and the content are in X". So, tree adds filename and structure around the blobs.  And also commits(the snapshot) points to the root tree(the whole project snapshot). And we know it stores metadata(author, date, message). and also remember git history.

Now let's say we commit again with changing to `Hello World V2`. Now git looks at which files were changed. If any files were changed, new blob is generated.  and git generates the new tree object point to project snapshot. and it points to new and old blob depending on what has changed. and then git creates new commit object(even if nothing is changed) pointing to new tree object and `.git/refs/head/master` is updated. The things to remember is that blob and trees are not re generated if nothing changed however commit isn't reused even if it doesn't change.


## 1. **Git Basics (Solid Foundation)**

- What is Git? (DVCS concept vs centralized VCS like SVN)
    
- How Git stores data (objects, blobs, trees, commits, refs)
    
- Local vs Remote repository
    
- Git configuration (user, global, repo-level configs)
    
- Basic commands
    
    - `git init`, `git clone`, `git status`, `git add`, `git commit`
        
    - `git log`, `git diff`, `git show`
        

---

## 2. **Branching & Merging**

- Git branches (what HEAD points to)
    
- Creating, switching, and deleting branches (`checkout`, `switch`)
    
- Merge types
    
    - Fast-forward merge
        
    - Recursive (three-way) merge
        
- Merge conflicts & resolution
    

---

## 3. **Git Internals (Deep Stuff)**

- Git object model
    
    - Blob, Tree, Commit, Tag
        
- How commits form a Directed Acyclic Graph (DAG)
    
- What `HEAD` really is
    
- Git refs (`refs/heads`, `refs/tags`, `refs/remotes`)
    
- Detached HEAD state
    
- Reflog (undo almost anything)
    

---

## 4. **Rewriting History**

- `git commit --amend`
    
- `git rebase` (interactive rebase: reword, squash, drop, reorder commits)
    
- `git reset` (soft, mixed, hard) vs `git revert`
    
- Cherry-picking commits (`git cherry-pick`)
    

---

## 5. **Working with Remotes**

- `git remote add`, `git fetch`, `git pull`, `git push`
    
- Difference between `pull` and `fetch` + `merge`
    
- Tracking branches (`origin/main` vs `main`)
    
- Upstream branches
    
- Forks vs clones
    

---

## 6. **Collaboration Workflows**

- Centralized Workflow (everyone commits to `main`)
    
- Feature Branch Workflow
    
- Gitflow (with `develop`, `release`, `hotfix` branches)
    
- Trunk-based Development
    
- Pull Requests (PRs)
    
    - Creating PRs on GitHub
        
    - Code reviews & approvals
        
    - Squash & merge vs Rebase & merge vs Merge commits
        

---

## 7. **Advanced Git Commands**

- Stashing (`git stash`)
    
- Bisect (`git bisect`) to debug commits
    
- Submodules (`git submodule`)
    
- Subtrees
    
- Hooks (`.git/hooks/`)
    

---

## 8. **GitHub Deep Dive**

- Issues & Discussions
    
- Projects (Kanban, task management)
    
- GitHub Actions (CI/CD workflows)
    
- GitHub Packages (registry)
    
- Branch protections & CODEOWNERS
    
- Managing large repos (LFS - Large File Storage)
    
- Security: Dependabot, secret scanning, signed commits (GPG)
    

---

## 9. **Team Practices & Real-World Scenarios**

- Handling merge conflicts in big teams
    
- Rebasing vs Merging in collaboration
    
- Squash commits for clean history
    
- Writing good commit messages (Conventional Commits)
    
- Managing releases & version tags
    
- Semantic versioning & changelogs
    

---

## 10. **Power User Level**

- Git plumbing commands (low-level: `hash-object`, `cat-file`, `update-ref`)
    
- Git replace objects (`git replace`)
    
- Partial clones, shallow clones
    
- Monorepo strategies (GitHub + Nx, Lerna, Turborepo)
    
- Debugging with `git fsck`, `git gc`
    

---
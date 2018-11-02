## Introduction

Make sure all participants have a Github account (and remember their passwords!). If they don't, they should be creating one during intro and during the stash section.

Lots of quirks and details to git, even for regular users. For example, only recently did I understand the [difference between an unstaged and untracked file](https://www.quora.com/What-is-the-difference-between-an-unstaged-and-an-untracked-file-in-Git).

----
## Review of Basic Git Commands
Commands to review: `git init`, `git status`, `git add`, `git commit`, `git log`, `git remote`, `git fetch`, `git pull`, `git push`, `.gitignore`

Terms to review: unstaged, untracked

----
## Understanding the Git Tree
For more advanced techniques in git, it is worthwhile to conceptualize your commit history as a tree.

A branch is really just a label on a specific commit. Best way to reason about the Git tree being static, then thinking of our location relative to the HEAD ref.

We call the HEAD ref detached when we move our HEAD (via `git checkout`, most commonly) to a non-branch tree.


----
## Undoing Changes
Introduce working directory, stage, commit history, `git commit --amend`, `git revert`, `git reset`.

## Reset, Checkout, Revert

|Command|Scope|Common use cases|
|:------|:---:|:-----|
|git reset|Commit-level|Discard commits in a private branch or throw away uncommited changes|
|git reset|File-level|Unstage a file|
|git checkout|Commit-level|Switch between branches or inspect old snapshots|
|git checkout|File-level|Discard changes in the working directory|
|git revert|Commit-level|Undo commits in a public branch|
|git revert|File-level|(N/A)|

https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting
https://www.atlassian.com/git/tutorials/undoing-changes/git-clean

#### Demo/Exercise: 

* Initialize a repo.
* Start editing a file called code.py.
* Add a couple of commits.
* Try to revert a previous commit.

```
    git revert HEAD
```
* What does the history look like?
    - you will see an extra commit: history is preserved.
* Add a few more commits.
* Reset to a previous commit.
```
    git reset HEAD~1
```
* What does the history look like?
    - the last commit is gone: history is rewritten.
    - file changes are still there

In some rare cases you want to make your local files look like a previous commit, i.e. you want to throw out the changes. In this case you can use the `--hard` prefix, but be careful, you will lose your changes.

```
    git reset --hard HEAD~1
```
Note: when reverting we used `HEAD` to refer to which commit we want to be removed, while when resetting we used `HEAD~1` to indicate we want to move to the content of one commit before the current commit.

https://www.atlassian.com/git/tutorials/undoing-changes
 

----
## Branches
So far the commit structure has been somewhat linear (one-dimensional). 

parent commit -> commit 1 -> commit 2 -> commit 3 -> revert commit 3 -> commit 4

It is quite hard to go back to an old commit and try out something new without affecting the current structure of your repository. Branches make this easy and allow for flexible collaborative workflows.

You have already used a branch: the master branch.

To check what branch you are on type

```
    git branch
```

You can create a new branch called `new_branch` with the command:

```
    git branch new_branch
```

Then you can switch to it using `git checkout`.

```
    git checkout new_branch
```

Shortcut for creating and switching to a new branch simultaneously:

```
    git checkout -b new_branch
```

To merge the changes in new_branch to master, first switch to master, and then run

```
    git merge new_branch
```

Once you are finished with a branch you can remove it with

```
    git branch -d new_branch
```

#### Exercise: Create and edit a local branch, then merge with master (fast-forward).

* Initialize a repo.
* Commit a file called `file.txt` with text `Parent commit` on the first line.
* Create a branch **my_experiment**.
* Switch to it.
* Add a line to `file.txt` with text `Feature commit 1` and commit the changes.
* Merge the branch with **master** (remember: you need to be on **master** to do that).
* Delete the branch.


#### Exercise: Create another local branch, but cause a merge conflict.

* Create a branch **another_experiment**.
* Add a line with text `Feature commit 2` and commit the changes.
* Switch to **master**, and add a line `Improvement of master`, and commit the changes.
* Now try to merge the **another_experiment** branch with master.

***You stumble into a merge conflict!***

----
## Merge Conflicts

#### Exercise: Go through steps to resolve the conflict.

* identify `<<<` and `>>>` symbols
* select blocks of code to preserve
* add file changes and commit

### Merging Strategies

How does git decide how to merge our files?

Many Possible Scenarios:
* two people add material at different places of the file
* two people change the same line in a document
* person A adds a line, person B deletes a different line

**3 Way Recursive Merge Strategy**

Simply looking at the difference of two modified files is not informative enough to decide which changes to keep. The 3 Way Recursive Merge Strategy looks also at the common ancestor of the files, i.e. it compares 3 states: the base, the source, and the target. 

![](3WayRecursiveStrategy.png)







Quiz (on how two files get merged)

## Switching Between Branches
What happens when we switch between branches?

Possible Scenarios:

* all files are committed on both branches
    - the files in the local folder get updated to the ones of the current branch
    
* a file tracked on both branches is changed on one of them but the change is not committed
    - switching is not allowed to prevent losing the data    
    
* a file is untracked on both branches
    - it remains intact

* a file is committed on one branch and untracked on the other branch
    - after switching file appears only on the branch it was committed

----
## Stashing

Usually it is safest to commit your changes before you switch between branches. But there are cases when you do not want to commit before switching: for example, you are in the middle of debugging on my_experiment but you want to fix a typo on master. Stashing can help in this situation.

`git stash` puts away any uncommitted changes (either staged or unstaged). For those familiar with CS data structures, it is a stack -- last in, first out using `git stash` and `git stash pop`. 

Other useful commands:
* `git stash save debugging_stash` - naming the stash
* `git stash list` - listing all stashes
* `git stash apply stash@{n}` - retrieving the stash without removing it from the stack

https://www.atlassian.com/git/tutorials/git-stash#stashing-your-work

#### Exercise: 

Part 1 (tracked files):

* add and commit all files on the master branch
* add and commit all files on my_experiment branch
* modify a file on branch my_experiment
* try to switch to master
* stash changes
* switch to master
* add a change
* go back to my_experiment and unstash the changes
* continue working

Part 2 (untracked files):

* create a file untracked.txt while on master branch
* switch to the my_experiment branch
* check whether untracked.txt still exists
* add and commit the file to my_experiment branch
* switch to master: is the file still there???

https://www.atlassian.com/git/tutorials/using-branches


-----
## Revisit of Git Tree
Show branches and merge using tree visualization.

## Collaboration Workflows

Different permission strategies, with a few different feature workflows.

### Permissions strategies: Centralized and Forking Workflows

The **Centralized Workflow** is simple. There is a central repository to serve as the single point-of-entry for all changes to the project. All users to whom you'd like to give *pull*, *push*, or *admin* permissions can be added as collaborators to the repository.

https://www.atlassian.com/git/tutorials/comparing-workflows/centralized-workflow

#### Demo: Show how to add users as a collaborator to a repository.

The **Forking Workflow** is a bit more complicated, but often used for open source projects. It allows for an individual or team to moderate the code being added to the central repository. Additionally, it enables anyone to submit code for inclusion -- not just those who were given permissions ahead of time.

To contribute, a user will fork the central repository, which creates a remote repository under my own Github account. For example, I may fork this upstream repository (**uwescience/git-intermediate-activity**) to my personal Github account (**bernease/git-intermediate-activity**). When I've completed changes on my fork, I can submit a pull request which will get reviewed, screened, and hopefully merged in by the maintainer.

https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow

### Development strategies: Simple, Feature Branching, and Gitflow Workflows

The **Simple (Forking) Workflow**, as defined by Bernease, is how we all started out. Have a single branch, the **master** branch where all new features and code changes are committed. Collaborating on a single branch will often necessitate rebasing or remembering to pull from the remote before any commits.

Any sizable features are created in new branches when following the **Feature Branching Workflow**. After the author is satisfied, they will merge their feature branch into **master**.

https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow

Lastly, the **Gitflow Workflow** is a structured workflow that's commonly used in large collaborations and open source projects. There are many branches compared to the previous two: a **master** branch with official releases, a **development** branch to merge in features and small fixes, a **release** branch for any pre-release work, and several **feature** branches for individual feature work.

https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

------
## Forks and Pull Requests

#### What if you want to contribute to a repo which you are not collaborator to?

#### Exercise: Fork a repo and create your first pull requests.

Use the repo importer to create a temporary copy of this repository for the purpose of the tutorial (change the name by adding the date). The students will practice contributing to this dummy repo, which could be deleted after the lesson. (This should be done before the lesson)

1. Show (best with two screens)
    - how to fork it, add a file, and submit a PR
    - how to review PR, discuss, request more details, and merge the PR.

2. Ask students to add a file to the repo named after their initials.

3. Try to merge all of them right away. Stress that if their PR is pending and they push to a repo, the new commits will be automatically added to the PR.

4. Ask all of them to work on modifying the same file.

    We will be working with the file [git-review.md](git-review.md).

    Assign a number to each participant and ask them to do the following:
    - respond to the corresponding question in their own words (if they have never seen the command they can google the answer but more important is their interpretation)
    - submit a PR with the modified file


***Did this activity cause some problems: why or why not?***

![](BasicForkWorkflow.png)

This was a very simple workflow in which you only work on master and all changes follow a line. But what if you want to work on different aspects of projects and we are not sure if in the end we will like our experiments to go into the main projects. It is still important to keep track of the changes so that we do not lose something important.


#### Exercise: Generate a pull request for a feature branch.

Inside of your fork (**YOUR_USERNAME/git-intermediate-activity**):

* Create a branch named: **YOUR_INTIALS_branch** (e.g., brh_branch).
* Write more text in the file named after your initials.
* Push to the corresponding public branch.
* Merge the branch through Github with the **master** branch of the repo from which you forked.

----
## Rebasing vs. Merging
Rebasing is a strategy to keep your git commit history more linear. Because rebasing changes history, *it should not be done on public repositories*!

Example using git tree.

https://www.atlassian.com/git/tutorials/merging-vs-rebasing

----
## More Advanced Git Topics (if time allows)

**Git Hooks**

https://www.atlassian.com/git/tutorials/git-hooks

**Git Large File Storage (LFS)**

https://www.atlassian.com/git/tutorials/git-lfs

**Advanced Git Log**

https://www.atlassian.com/git/tutorials/git-log

**Refs and Reflog**

https://www.atlassian.com/git/tutorials/refs-and-the-reflog

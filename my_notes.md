### Questions
- [ ] How to walk back from a commit (move master pointer back to earlier commit?)

- [ ]
---

### Misc Notes
#### Contributing to a Project
[chapter 5.2](https://git-scm.com/book/ch5-2.html) from official git reference
Tip: use `git diff --check` to identify annoying whitespace errors before committing.

**Commenting guidelines**:
- Generally, top line is summary of max 50 characters, then white space, then detailed explanation (if necessary)



---
### From [Learn Git Branching](http://pcottle.github.io/learnGitBranching)


**New commit**: `git commit -a -m <msg>`

Git enthusiasts:
> Branch early, branch often

"Easier to logically divide up your work than have big beefy branches."

**New branch**:
`git checkout -b <new_branch>` (`git branch <new_branch>` + `git checkout <branch>`)

**Merge**: `(branchA) git merge <branchB>` creates a special commit that has two unique parents. "Merges branchA into branchB".
- Exception: "Fast-forward" merge occurs when a branch is merged into one of its children, in which case only moving the active branches' pointer is necessary.

Example:

   (0)     |    (1) reg. merge     |      (2) fast-forward merge        |
:---------:|:-----------------:|:---------------- :
 start     | `(master) git merge bugFix` | `(bugFix) merge master`
"merge... ->"   |      bugFix into master     |master into bugFix
![before merge][bM] | ![simple merge][sM]  |  ![fast forward merge][ffM]

[bM]: images/before_merge.png
[sM]: images/simple_merge.png
[ffM]: images/ff_merge.png

**Rebasing**: Like merge, combines work across branches, but instead "copies" them, and plops them down somewhere else.
- Used to make a linear sequence of commits, so that log is more readable (otherwise displays parallel commits in chronological order).

Example:

   (0)     |    (1) reg. rebase     |      (2) fast-forward rebase        |
:---------:|:-----------------:|:---------------- :
 start     | `(bugFix) rebase master` | `(master) git rebase bugFix`
"rebase... ->"   |  bugFix onto master       | master onto bugFix
![before merge][bRB] | ![simple merge][sRB]  |  ![fast forward merge][ffRB]

[bRB]: images/before_rebase.png
[sRB]: images/simple_rebase.png
[ffRB]: images/ff_rebase.png

#### Navigating Git

**HEAD**: currently checked out commit, determines working directory. Usually points to a branch show in parenthesis on cmd like `(master) <current-dir> $`.
- Detached HEAD: when a specific commit has been checked out (not a branch). Changes can be saved into a new branch with `git checkout -b <new_branch>`, otherwise if `checkout` is done, those changes will be lost.

**Relative Refs**: moves HEAD relative to current place.
- For branches: `git checkout master^` moves HEAD to parent of master (`master^^` to grandparent, etc.)
- For detached HEAD: `git checkout HEAD^`, etc.

**Branch forcing**: directly reassigns a branch to a commit with `git branch -f <branch> <commit>`
- Example: `git branch -f master HEAD~3` moves master branch three parents up from HEAD.

#### Reversing Changes in Git
**Reset**: moves a branch backward `n` branches as if it never existed with `git reset HEAD~n`. Does not create new commit; only use locally.

**Revert**: creates new commit for reversion to previous commit with `git revert HEAD`



---
### From [Visualizing Git Concepts with D3](http://onlywei.github.io/explain-git-with-d3)

`git reset origin/master` restores local branch to state on origin.
* Will not delete untracked files, use `git clean -df`

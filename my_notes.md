### Questions
- [ ] How to walk back from a commit (move master pointer back to earlier commit?)

- [ ] More details on how `git revert` works

- [ ] How to use `git rm --cached` to remove file from tracking

- [ ] What does the "-u" in `git push -u origin master` do? [StackOverflow](http://stackoverflow.com/questions/5697750/what-exactly-does-the-u-do-git-push-u-origin-master-vs-git-push-origin-ma)

---

### Misc Notes
#### Contributing to a Project
[chapter 5.2](https://git-scm.com/book/ch5-2.html) from official git reference.

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

   (0)     |    (1) reg. merge     |  (2) fast-forward merge        |
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

**Relative Refs**: Moves HEAD relative to current place.
- For branches: `git checkout master^` moves HEAD to parent of master (`master^^` to grandparent, etc.)
- For detached HEAD: `git checkout HEAD^`, etc.

**Branch forcing**: directly reassigns a branch to a commit with `git branch -f <branch> <commit>`
- Example: `git branch -f master HEAD~3` moves master branch three parents up from HEAD.

#### Reversing changes in Git
**Reset**: moves a branch backward `n` branches as if it never existed with `git reset HEAD~n`. Does not create new commit; only use locally.

**Revert**: creates new commit for reversion to previous commit with `git revert HEAD`

#### Moving work around
**Git cherry-pick**: `git cherry-pick <Commit1> <Commit2> <...>` allows for specific commits to be appended to HEAD (as long as they are not ancestors of HEAD, i.e. must be in different branches). Similar to rebase, just more specific. See interactive rebase when commit ids are not easily known.

**Git interactive rebase**: `git rebase -i <commit>` opens list of commits in editor to Reorder commits, omit commits, squash (combine) commits (omitted in this tutorial)
- Selected commits are appended onto to `<commit>`

#### Misc. tips, tricks and techniques
##### Locally stacked commits
*Problem*: Fixed bug in separate branch from master, and now want to merge fixed code into master branch without code added for debugging.

*Solution*: Use `git cherry-pick` or `git rebase -i` to get `bugFix` (or just C4) onto `master` branch. "Make sure master receives the commit that bugFix references."

method     |    (0) start    |     (1) end        |
:---------:|:-----------------:|:---------------- :
Methods separated by `reset` (specific for online simulator) ![code][case1-code] NOTE: `cherry-pick` method leaves bugFix branch at C4 (different from end diagram) | ![start][case1-start]  |  ![end][case1-end]

[case1-code]: images/case1_code.png
[case1-start]: images/case1_start.png
[case1-end]: images/case1_end.png

##### Juggling Commits

*Problem*: Small change needed to a past commit, not most recent (like slight change in image dimension, where new image was added, then caption for that image, both in separate commits).

*Solution*: Need to amend that commit with `git commit --amend`, which replaces the tip of the current branch with a new commit. But first, rearrange so that target commit is at tip, then put back once amended.
- Can rearrange with `git rebase -i` or, in case of rebase conflict, use `git cherry-pick`.

method     |    (0) start    |     (1) end        |
:---------:|:-----------------:|:---------------- :
![code][case2-codeA] --- NOTE: `undo` indicates a mistake in merging (didn't actually need to be undone). Does not include interactive rebase text activity. | ![start][case2-start]  |  ![endA][case2-endA]
![code][case2-codeB] --- NOTE: `undo` indicates a mistake in merging (didn't actually need to be undone). Does not include interactive rebase text activity. | ![start][case2-start]  |  ![endB][case2-endB]

[case2-codeA]: images/case2_codeA.png
[case2-codeB]: images/case2_codeB.png
[case2-start]: images/case2_start.png
[case2-endA]: images/case2_endA.png
[case2-endB]: images/case2_endB.png

###### Using Git tags
Quick recap:
>As you have learned from previous lessons, branches are easy to move around and often refer to different commits as work is completed on them.
> Branches are easily mutated, often temporary, and always changing.

`git tag <tag> [commit]` (goes to HEAD by default)
`git checkout <tag>` into detached HEAD state on commit.

*Idea behind it*: Tags are used to permanently mark historical points in your project's history. E.g. major releases and big merges. Note: more permanent than a branch (only 'detached' HEAD stae, so can't checkout and modify in the same way as branches)

`git describe <ref>`, `<ref>` being anything that resolves to a commit (default is HEAD).

The output of the command looks like: `<tag>_<numCommits>_g<hash>`
, where:
*  `<tag>` is the closest ancestor tag in history,
* `<numCommits>` is how many commits away that tag is, and
* `<hash>` is the hash of the commit being described.

##### Rebasing over 9000 times
* Pro tip: checkout branch you are trying to rebase, and then run destination in rebase command.
* Pro tip: checkout branch you are trying to move into the destination branch for the merge. Then, merge, specifying destination in rebase command.

##### Specifying Parents




---
### From [Visualizing Git Concepts with D3](http://onlywei.github.io/explain-git-with-d3)

`git reset origin/master` restores local branch to state on origin.
* Will not delete untracked files, use `git clean -df`

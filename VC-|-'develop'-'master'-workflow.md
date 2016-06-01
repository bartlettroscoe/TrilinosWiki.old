**Table of Contents:**
* [Overview](#overview)
* [Get on a local 'develop' tracking branch](#get_on_local_develop)
* [Simple Centralized workflow on the 'develop' branch](#centralized_develop_workflow)
* [Transitioning local changes to the 'develop' branch](#transition_to_develop)

<a name="overview"/>
# Overview

The Trilinos project uses a long-lived branch called 'develop' to conduct basic development.  All Trilinos developers directly pull from and push to the shared github 'develop' branch.  Then, the 'master' branch is updated from the 'develop' branch when it passes a specific set of builds for a specific set of packages (the set of packages and builds will evolve over time).  This is depicted in the below figure:


![Triinos Git 'develop'/'master' workflow](https://github.com/trilinos/trilinos_wiki_images/blob/master/GitDevelopMasterWorkflow.png)

The motivation for the usage of a 'develop' branch and the full set of mechanics and implications are described in [Addition of a 'develop' branch](https://docs.google.com/document/d/1uVQYI2cmNx09fDkHDA136yqDTqayhxqfvjFiuUue7wo/edit#heading=h.u2ougk1wk7ph).

However, this page contains just the information that an average Trilinos developer needs to know in order to transition to and use the 'develop' branch as well as make commits on the local 'develop' branch and push to the shared 'develop' branch.  The level of git knowledge to perform these tasks should be very low and all of the commands and steps are described in detail below.

(NOTE: The "bug-fix" workflow elements are not described below but are described in great detail the above reference.  If things go well, then these "bug fix" commits should almost never be needed.  But in the rare cases these "bug-fix" commits are needed/desired, then more experienced Trilinos git developers can help make these changes.)

<a name="get_on_local_develop"/>
# Get on a local 'develop' tracking branch

Modifying the workflow from the [Simple Centralized Workflow](https://github.com/trilinos/Trilinos/wiki/VC-|-Simple-Centralized-Workflow) to use the 'develop' branch instead of the 'master' branch is easy.  If the local repo is clean (i.e. no local modifications or untracked files), then one just needs to get on a local 'develop' tracking branch.  If the local 'develop' tracking branch has not already been created, then create it using:

```
$ cd Trilinos/
[ (master)]$ git fetch origin
[ (master)]$ git checkout --track origin/develop
[(develop)]$ 
```

If the local 'develop' tracking branch was already created (can be seen by running `git branch`), then just check out that branch using:

```
$ cd Trilinos/
[ (master)]$ git checkout develop
[(develop)]$ 
```

<a name="centralized_develop_workflow"/>
# Simple Centralized workflow on the 'develop' branch

Once on the local 'develop' branch (which is tracking the remote 'origin/develop' branch), one can simply use the [Simple Centralized Workflow](https://github.com/trilinos/Trilinos/wiki/VC-|-Simple-Centralized-Workflow) using raw `git pull`, `git commit` and `git push` commands, i.e.:

```
[(develop)]$ git pull                               # from origin/develop
[(develop)]$ emacs <files>                          # or using other editor
[(develop)]$ git commit -a
[(develop)]$ git status
[(develop)]$ git log --name-status @{u}..HEAD
[(develop)]$ git pull                               # from origin/develop
... Test changes (e.g. using checkin-test.py) ...
[(develop)]$ git pull --rebase                      # from origin/develop
[(develop)]$ git push                               # to origin/develop
```
NOTE: The checkin-test.py script performs the steps from the second `git pull` after the local modifications are committed to the final `git push` robustly in a single command.

<a name="transition_to_develop"/>
# Transitioning local changes to the 'develop' branch

If a Trilinos developer does not have any local changes that have not yet been committed and pushed to Trilinos 'github' master branch, then simply creating a local tracking `develop` branch as described [above](#get_on_local_develop) is all that is required and then one can go back to regular development activities but now on the 'develop' branch.  However, if a Trilinos developer as already made changes (and perhaps local commits) to the local git repo's 'master' branch, then these changes will need to be transferred over to the 'develop' branch before they can be pushed to the github 'develop branch.   Also, if a developer later forgets and accidentally makes changes on the local 'master' branch, then these changes need to be transferred over to the 'develop' branch as well.

The following instructions will help a developer transition their existing local git repos over the the 'develop' branch smoothly and safely.

NOTE: These instructions assume that the Trilinos develop is just performing the [simple centralized workflow on the local 'master' branch](https://github.com/trilinos/Trilinos/wiki/VC-%7C-Simple-Centralized-Workflow) and is therefore has just been directly pushing their locally created commits directly to the github 'master' branch.  If a more complex workflow is being used (such as using a [shared topic branch](https://docs.google.com/document/d/1uVQYI2cmNx09fDkHDA136yqDTqayhxqfvjFiuUue7wo/edit#heading=h.eezqw2tso48u), or other types of git workflows), then those Trilinos developers should know enough about git in order to make the transition safely.

But before getting started with this transition, every Trilinos developer should set up their local account to use the git usability scripts git-prompt.sh and git-completion.bash and enable git `rerere` as described [here](https://github.com/trilinos/Trilinos/wiki/VC-%7C-Initial-Git-Setup).  The shell scripts will make it obvious what local branch a developer is on and git `rerere` will help with the rebasing and merging commands required to transition local commits.

The process to transition local changes not on the 'develop' branch to the 'develop' branch are:

**1) Commit any desired uncommitted changes:**

```
[(master)]$ git add <modified-or-new-files>
[(master)]$ git commit
```

If there are any remaining changes to tracked files that one does not want to keep, then they can be reverted using:

```
[(master)]$ git checkout HEAD -- .
```

If there are untracked files that one does not want to keep, then just delete them manually or do:

```
[(master)]$ git clean -xdf
```

or add ignores for them.

**2) Checkout the local tracking 'develop' branch:**

This step is already described [above](https://github.com/trilinos/Trilinos/wiki/VC-%7C-'develop'-'master'-workflow#get_on_local_develop).
That is, either one creates a new local tracking 'develop' branch from scratch (if it has not already been created) with:

```
[ (master)]$ git fetch
[ (master)]$ git checkout --track origin/develop
[(develop)]$
```

or one checks out the previously created 'develop' tracking branch and updates it with:

```
[ (master)]$ git checkout develop
[(develop)]$ git pull   # from origin/develop
[(develop)]$
```

**3) Merge changes from the local 'master' branch to the local 'develop' branch:**

```
[(develop)]$ git merge master   # resolve any merge conflicts
```

At this point, the merged in commits from the local 'master' branch as safe and will get pushed to the remote github 'develop' branch.  These commits will be rebased before the final push as described [above](#centralized_develop_workflow).  (NOTE: If git rerere is enabled, then any merge conflicts that have already been resolved will be resolved automatically on the final rebase before the final push.)

**4) Reset the local 'master' branch**

If one will later want to go back to the local 'master' branch, then it is a good idea to reset it to the 'origin/master' branch  **after** the locally updated 'master' branch has been merged into the 'develop' branch as described above.  This reset is performed as:

```
[(develop)]$ git checkout master
[ (master)]$ git reset --hard origin/master
```

Then one can go back to the 'develop' branch and continue work there with:

```
[ (master)]$ git checkout develop
[(develop)]$
```

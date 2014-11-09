# Git Notes

##Diffs

    git diff # Shows diff between unstaged and last commit
    git diff —staged # Shows diff between staged and last commit

## Rollback and Ammending
    git reset file #Unstage a file

    git reset HEAD^ #Undo the last commit and ‘add’

    git reset —soft HEAD # The soft flag undoes the last commit but leaves the files in staging.

    git reset —hard HEAD^ # Kill the last commit entirely

The caret says “move to the commit one before the current head”. HEAD^^ refers to two commits ago.

    git checkout — FILENAME # Reset file contents to that of last commit

    git commit —amend -m “Message” # Updates the last commit with any files currently staged (and overwrites the message)

## Remotes and Push/Pull
    git remote add <somename> <someurl> # Adds the specified url as a remote, named <somename>.
Note: It’s usual to refer to the canonical repo as origin.

    git remote -v # See all the remotes our repo knows about.

    git push <remote url> <local branch to push>
The -u option makes this the default remote and branch to push to.

    git pull # Pull everything down

NOTE: don't screw with the history by removing or amending commits after you’ve pushed up!

## Branching
Note that local branches are different to remote branches

    git branch foo # Create local branch foo

    git branch # List all local branches

    git checkout foo # Switch to foo branch (wether local or remote)

    git checkout -b newthing # Shortcut to create and checkout a new local branch

    git merge foo # Merge the foo branch into the current branch

    git branch -d foo # Delete local branch foo

    git branch -r # Show remote branches

    git remote show <branch> # See details of all branches (remote and local)

## Collaborating

    git fetch # Fetch down the remote stuff, but don't merge it into my local branch

    git merge origin/master #Merge the remote master branch we pulled down into the local master

    git pull # Does the previous two commands at once. 

    git remote prune origin # Remove any stale references to remote branches that have been deleted.

    git push <remote> <local_branchname>:<remote_branchname> # Push to a different remote branch

    git push origin :foo # Delete the remote branch foo (works by pushing nothing to the remote branch)

### Tagging
    git tag # List tags

    git checkout <tagname> # Switch to a tag

    git tag -a <tagname> -m “Message” # Create a tag

    git push —tags # Needed to push tags up to a remote

## Rebasing
    git fetch # Pulls down remote stuff but doesn’t merge into local branch.
Rebase avoids merge messages. 

    git checkout feature # Move to feature branch
    git rebase master # Run all the commits from master against feature branch
    git checkout master
    git merge feature # Merge feature into master - there will be no conflicts

    git rebase —continue # Carry on with a rebase after 

Example:

    git fetch
    git rebase origin/master # Move my commits to after those made on the central repo

    git rebase -i HEAD~3 # Open an interactive rebase script with the last three commits in it.
To reorder commits, just swap the lines about.

To split the last commit:
````shell
git rebase -i HEAD^
# set the line to edit
git reset —soft HEAD^ # Rollback the last commit
git reset myfile # Remove a file from this commit
git commit -m “Stuff without the commit”
git add myfile
git commit -m “Added myfile”
git rebase —continue
````

## Stashing
    git stash save #Save the local changes since the last commit
    # Do some stuff
    git stash show # See some info about the last stash
    git stash apply # Bring the changes back
    git stash drop #Bin the last stash off the top of the stack

    git stash pop #Runs stash apply then stash drop

    git stash save —untracked-files # Also stash files not tracked by git yet

    git stash list # Show all the stages along with the commit before they were applied and their name
    git stash apply stash@{3} #Apply stash number 3 in the list

    git stash clear # Clear all stashes

## Rewriting History
    git clone myrepo; cd myrepo # Make a backup
    git filter-branch —tree-filter <some shell command to run>

    git filter-branch —tree-filter ‘rm -f password.txt’ # Remove password.txt from every commit
    git filter-branch —tree-filter ‘rm -f password.txt’ —all # Remove password.txt from every commit in every branch

Note: filter-branch can take —prune-empty, which will remove any empty commits.

## Configuration
.gitattributes file:
````
*.rb     text=auto # Decide on wether the file is text or not. Use the default settings to handle it.
*.css   text # Always treat this as text
*.bat   eol=crlf # Always add a CR and LR (Windows)
*.sh    eol=lf # Always add a LR (UNIX)
*.jpg   binary # Always treat as a binary file and add nothing.
````

## Cherry Picking
How do we pick certain commits from one branch into our production branch?

    git cherry-pick XXXXX # Cherrypick this ha from another branch.
    git cherry-pick —edit XXXXX

    git cherry-pick —no-commit XXX XXX XXX # Pull in these commits, but do not commit them to the current branch.

The -x option appends the original ha to the message.
The —signoff option appends the name of the person doing the cherry-pick.

## Submodules

Presuming a submodule exists on GitHub, within the parent project:

    git submodule add http://path.to.submodule.repo.git # Clone submodule into project

If we pull or clone a project with submodules, we need to pull them down…

    git submodule init
    git submodule update

## Reflog
Note that reflogs are only local, they're never pushed up to shared repos.

    git reflog # See all changes to the system, including commits we’ve rolled back. The reflow isn

If I've done a hard reset and blown away some changes that I now want back, I can get them again by doing a hard reset on the SHA of the change shown in reflog.

    git log --walk-reflogs # Look at all reflogs as well as commits.

# GitHub

## Working with an Upstream fork (For open source patches etc)


Assuming I’ve forked a repo that I don’t own and am working locally, pushing to that repo, how do I get any changes from the original (upstream) repo? I can create a new remote (convention calls it upstream) to pull from:

````shell
git remote add upstream <path.to.original>
git fetch upstream
git merge upstream/master master
git push origin master # Push to my fork.
````

I could also `git pull upstream/master master` instead of fork then merge

## Working on a Single Repo (usual github flow)

We already know how to use the feature branch workflow with pull requests to merge into master, but here are some best practices when doing that:

- Interactive Rebase
- Rebase
- Recursive MErges

Before committing

1. Fix up anything discussed on the pull request
2. Squash, Fix up or reorder commits

    git rebase -i feature_branch
    git push -f

3. Rebase on Master

    git fetch
    git rebase origin/master
    git push -f

4. Confirm that the ranch passes on the CI environment.
5. Merge into recursive merge into master (ie. don’t fast-forward. This ensures that there is a specific commit message detailing the merge. If we’re just hotfooting something, we may want to use a fast-forward merge instead)

    git checkout master
    git merge —no-ff feature_branch
    git push

6. Check the PR has been closed.

## Release Tags

git tag # Just a plain old tag to refer to a certain commit
git tag -s # A securely signed tag, showing who made it
git tag -a # An annotated tag

To create a tag when pushing into production:

    git checkout XXXX # The commit to tag on
    git tag -a v.1.2.3 -m “v1.2.3 - Deploying certain feature to production”
    git push —tags
    git checkout master # Or whatever branch we were on.

Note: v1.2.3 uses semantic numbering:
- 1 = Major. Large change to application, might break backwards compatibility.
- 2 = Minor. Change to user functionality
- 2 = Patch. Small change that doesn’t add any valued functionality.

## Release Branches

We can use branches to make specific pre-production changes to a branch before releasing to production. Here are some reasons we might want to use release branches rather than tags:

1. **Loads of Manual QA** - This allows the testers to do their QA on the release branch and developers to make hot fixes. We can the release to production and merge the hot fixes back into master.
2. **Long Running Releases** - If we’re having to support more than one version of the codebase on production, we’ll want to keep a release branch live so we can make security fixes and updates to that particular release.
3. **On the fly** - If we need to quickly make pre-release hot fixes which will be merged into master once the release has been made.

## GitHub Releases

Github allows us to add descriptions, release notes and binary files (like compiled source) to a release. We can use the releases tag to create one based on an existing tag, or to create a new tag/release from any commit.

## Github Issues

We can mention issues in commit messages to have them included in the issue history: `#1`, for example. If we use keywords like `fixes #2` or `closes #2`, the issue will be closed when this commit is merged into master.



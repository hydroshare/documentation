## Git for HydroShare Developers

### TL;DR

New feature branch

    $ git checkout develop && git pull
    $ git branch <issue_no.>-<branch_desc>
    $ git checkout <issue_no.>-<branch_desc>
    ...
    $ git push --set-upstream origin <issue_no.>-<branch_desc>
  
Commit with issue reference

    $ git commit -m "[#<issus_no.>] commit_message"
    $ git push origin <issue_no.>-<branch_desc>

Rebasing

    $ git fetch
    $ git rebase origin/develop # See below for conflict resolution
    
Creating pull request from [Github](https://help.github.com/articles/using-pull-requests/)  

---

### New feature branch

Using the hydrodev VM the hydroshare git repository would be cloned into the home directory for the hydro user.

    $ git clone https://github.com/hydroshare/hydroshare.git
    
At this point there will be a hydroshare directory in the `/home` directory. Change into this directory, execute a git pull, and verify that you are on the master branch.

    $ cd /home/hydroshare
    $ git pull
      From https://github.com/hydroshare/hydroshare
      ...
      Already up-to-date.
    $ git branch
    $ git branch
      * master
      
Next you will want to check out the **develop** branch, and make a local copy of it using the [Github issues](https://github.com/hydroshare/hydroshare/issues) number that identifies the feature you will be working on. In this example, issue number **21** will refer to **your feature description** as posted in the Github issues title.

    $ git checkout develop
    $ git branch
      * develop
      master
    $ git branch 21-your-feature-description 
    $ git checkout 21-your-feature-description
    $ git branch
      * 21-your-feature-description
      develop
      master

At this point you will be on your new **21-your-feature-description** branch and are ready to push this new branch to Github. Because you made this branch from the cloned hydroshare repository, git understands that **origin** refers to the github.com/hydroshare/hydroshare repository, but Github has no notion of your newly created local branch. You are now ready to push your new repository to Github.

    $ git push --set-upstream origin 21-your-feature-description
      Counting objects: ##, done.
      ...
      To https://github.com/hydroshare/hydroshare.git
      * [new branch]      21-your-feature-description -> 21-your-feature-description
      Branch 21-your-feature-description set up to track remote branch 21-your-feature-description from origin.

You are now ready to add new code to your feature branch, and be able to push changes to your feature branch on Github

    ... new code
    ...
    $ git push origin 21-your-feature-description


### Commit with issue reference

Feature development branches should always be named as and referenced to a particular [Github issue](https://github.com/hydroshare/hydroshare/issues). Because this is the case it is useful to refernce this issue when making commits to the repository.

This is accomplished by inserting a reference to the issue number at the beginning of your commit comment in `"[#<issue_no.>] commit-message..."` format.

Using our example featue branch **21-your-feature-description**

    $ git commit "[#21] your-commit-message-goes-here"
    
The reference of this commit to issue number 21 can now be found in the Github issues entry online as well as the git logs.

### Rebasing

What is rebase?

- Rebase means taking commits made to an older revision of a branch and applying them to a newer revision of that branch (usually when the origin branch has changed while we were commiting).
- Rebase is an alternative for merge, it's main advantage over merging is that it allows you to maintain a clean linear history.

[Tutorial](https://www.atlassian.com/git/tutorials/rewriting-history/git-commit--amend)

As you develop on your feature branch, it is likely that others are also developing on their own feature branches, and possibly having the changes they've made merged back into the develop branch from which your feature branch was initially based upon. It is a common practice in this case to regurlarily **rebase** your branch to incorperate any of the changes that had been made to the develop branch without having to carry along the git commit history made by others along the way.

In order to acccomplish this, you would want to make sure that the develop branch in your local repository is up-to-date with the one on Github. Then you will rebase your feature branch to incorperate any changes that may have been made.

Assuming that you are on your feature branch

    $ git branch
      * 21-your-feature-description
      develop
      master
      
    # fetch the latest references for all branches in the repository, then rebase
    $ git fetch
    $ git rebase origin/develop

**CONFLICT resolution**

It is possible that in the time between your last rebase and current attempt that something within the develop branch changed to the extent that it cannot be automatically rebased. When this occurs it is necessary to manually merge the conflicted files using a merge tool, or by editing the affected file(s) directly.

The hydrodev VM has [kdiff3](http://kdiff3.sourceforge.net) installed on it for the purposes of merging conflicted files.

    $ git fetch
    $ git rebase origin/develop
  
    First, rewinding head to replay your work on top of it...
    Applying: added another line to file4
    Using index info to reconstruct a base tree...
    Falling back to patching base and 3-way merge...
    Auto-merging <conflict_filename>
    CONFLICT (content): Merge conflict in <conflict_filename>
    Failed to merge in the changes.
    Patch failed at 0001 added another line to <conflict_filename>

    When you have resolved this problem run 
        "git rebase --continue".
    If you would prefer to skip this patch, instead run 
        "git rebase --skip".
    To restore the original branch and stop rebasing run 
        "git rebase --abort".

Resolving the conflicted file(s) using a mergetool such as kdiff3 will then allow you to use the `--continue` option and rebase successfully.

    # refer to the kdiff3 documentation for use
    $ git mergetool -t kdiff3
    Merging:
    <conflict_filename>

    Normal merge conflict for '<conflict_filename>':
      {local}: modified file
      {remote}: modified file
    Hit return to start merge resolution tool (kdiff3):

If for some reason you are not able to resolve the conflicts, use the `--abort` option and email the dev-team for help.


### Creating a pull request from Github

There is documentation on Github regarding [using pull requests](https://help.github.com/articles/using-pull-requests/) to request that the work you've perfomed in your feature branch be brought into the develop branch.





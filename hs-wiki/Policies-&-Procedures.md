This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide). It describes standard release procedures and other policies for contributing to Hydroshare.

# Contributing code

This section documents the standards we employ within the core development group for contributing code and getting things into the dev and production environments.

The [hydroshare](https://github.com/hydroshare/hydroshare) project contains what is necessary to run Hydroshare, but it should not contain any "extra bells and whistles." With this theory in mind, please follow the following practices:

* Code that introduces new features into Hydroshare should be encapsulated into a Django app.  As features are completed, they will be rolled in as submodules in the [hydroshare](https://github.com/hydroshare/hydroshare) repository.  


## Branching procedures

We use GitHub Flow as our standard Git workflow. GitHub recommends reading [Scott Chacon's article](http://scottchacon.com/2011/08/31/github-flow.html) to learn more. This article summarizes GitHub Flow as follows:

* Anything in the master branch is deployable
* To work on something new, create a descriptively named branch off of master (ie: new-oauth2-scopes)
* Commit to that branch locally and regularly push your work to the same named branch on the server
* When you need feedback or help, or you think the branch is ready for merging, open a pull request
* After someone else has reviewed and signed off on the feature, you can merge it into master
* Once it is merged and pushed to ‘master’, you can and should deploy immediately

## Migrations
1) Missing migrations must be committed and merged

2) The makemigrations command must be a part of creating a new branch, not deployment (running them should be part of deployment, though)

3) No branch or pull request will be accepted for merge that does not provide a complete set of migrations. No branch should be accepted for which the makemigrations command would try to generate anything that isn't already included.

## Coding standards

Please adhere to [PEP8 standards](http://legacy.python.org/dev/peps/pep-0008/) where possible. This is to ensure that everyone can read your code. Line lengths need not be followed strictly, however.

Code comments should be in [Markdown format](https://help.github.com/articles/github-flavored-markdown) for better integration with this Wiki site and for the ease of our technical writer in extracting your comments when they need to become part of the documentation. 

## Deployment and libraries

If you add a library to your project, add it into the `requirements.txt` file for your app.  You can do this by executing `pip freeze > my_app_name/requirements.txt` in your hydroshare project and committing the resultant file to the project. 

If you add a library or package to your version of the hydroshare container, please do your best to use `apt-get` to install the library and not rely on custom code.  If you must rely on custom code, include the tarball for your library as part of your git repository and add build instructions.  

**If a new package has to be added to the container,** please add this task as a Chore in the [Hydroshare project on Pivotal Tracker](https://www.pivotaltracker.com/projects/703523/overview) and assign it to Jefferson Heard.  

**Do not push your container back to the hydroshare repository** as Docker is a new technology and not as resilient as `git`.  I don't trust it to merge images from different people.  

# Tracking tasks

Please check every week for tasks in your queue on [Pivotal Tracker](https://www.pivotaltracker.com/projects/703523/overview). You can search via your initials or find a task with your initials on it in the backlog, click on your initials, and all tasks assigned to you will come up in their own separate list.

If you are working on something, please don't forget to click "Start" so we know and can keep track of status.  

If you finish something, click "Finish".  

If you check something into git that completes a task, click "Deliver".

**Do not click accept yourself if you are the person who completed the task.** Either the PIs on the project or the original requestor will sign off on the task's completion.  This keeps us from losing track of things getting completed, and it keeps people in the loop who are waiting for something to be completed before they go about their business.  

**Do not put anything from the icebox into the backlog unless it can be worked on without completing other tasks.**  The backlog is for tasks that are currently actionable.  If someone has to complete a task before your task can be worked on, that task goes in the Icebox.  

Thanks, all!

-- The Management 

## Docker container policy

As a general practice, **do not modify the docker containers on your own.** If you need certain Linux software packages installed to run on the Hydroshare server, or you otherwise want to modify the docker containers, instead please create a story on [Pivotal Tracker](https://www.pivotaltracker.com/projects/703523/overview) or (if you are not a member of the dev team) [raise an issue](https://github.com/hydroshare/hydroshare2/issues) here on GitHub making your request, and the container will be modified and re-issued.
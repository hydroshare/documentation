#### ADVANCED: do not use this document unless you have been authorized to do so by the project lead.

To push a new container to the repository, do the following as `root`:

> Can the steps be modified to use clean git repository copy, on a clean database (./initdb)?
> This should help avoid issues with missing files in the repository, and bad migrations - dv


    $ cd hydroshare # my working directory for the hydroshare github repository
    $ ./stop-all-containers
    $ docker commit hydroshare-dev dev.hydroshare.org:5999/hydroshare-dev
    $ docker push dev.hydroshare.org:5999/hydroshare-dev
    $ docker start hydroshare-postgis
    $ docker start hydroshare-redis
    $ ./run-testserver (or ./run-webserver)

## Testing schedule: 

Wednesdays before call: Finalize any container changes, shutdown and commit this week's container.  Bring containers back up before call.  

If changes have caused a new container to be necessary, then tag the container as the latest (should be automatic) and push to the repository.  The repository saves old versions, so you shouldn't be able to overwrite a container by accident. 

## Postgis and Redis containers

These generally should not change.  They are pulled from the Docker repository at [http://docker.io].  If they need to change because of version changes, the project lead should approve updating the postgis and redis containers.  This will involve downloading and building the containers, then labeling them as hydroshare-postgis and hydroshare-redis.  Then push them up to the repository as dev.hydroshare.org:5999/redis or postgis respectively.  

Before upgrading the database or redis containers, be sure to do a dump of the databases to a local file and reload later.  Do not obliterate the old container until you're sure your database dumps will load.  Both redis and postgis have database dump tools you can use.

# Release QA (in progress)
1. in a VM rollback to postgis and redis containters.
1. pull hydroshare container
1. check permissions (first check)
    1.  'find . -group root'  [not owned by root](https://www.pivotaltracker.com/story/show/65917962) 
    1. any executable scripts are executable
1. createdatabase
1. runtests script
1. run-server script
    1. see if site works
1. check permissions second
    1. 'find . -group root' [not owned by root](https://www.pivotaltracker.com/story/show/65917962) if permissions have changed due to a script or other step.
1. ssh to hydroshare-dev and see that environment variables are there [ssh issue](https://www.pivotaltracker.com/story/show/66776926)
    1. (*_TCP, etc are there)
1. check to be sure username is removed from git [git username issue](https://www.pivotaltracker.com/story/show/64864496)

The above might also be done on a base image with a pull to see that migrations work.

# smoketests
 need to have some script that runs some tests via a browser.
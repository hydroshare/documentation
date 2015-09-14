Setting up Shared Folders with boot2docker - Windows
====

This is a companion document to the 

This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide)

# Documentation conventions

There are two terminal windows that will be used to interact with the hydrodshare code. The first is the **_Git-Bash_** terminal that is used to make all Git command line calls on the host system. The second is the **_boot2docker_** terminal that pops up when the "_Boot2Docker Start_" alias is used to launch boot2docker.

In general, all Git related activity should be done from the **_Git-Bash_** terminal, and all docker or fig related activity should be done from the **_boot2docker_** terminal.


# 1. Prerequisites

* Git-Bash [Install](http://git-scm.com/download/win)
* PyCharm [Install](http://www.jetbrains.com/pycharm/download/)
* Boot2Docker [Install](http://docs.docker.com/installation/windows/)
* (optional) a graphical Git client which supports submodules such as [Sourcetree]( http://www.sourcetreeapp.com/)


# 2. Setup

This document will get you setup with a Hydroshare development environment.  The configuration of this environment exactly reflects the configuration on `beta.hydroshare.org` and `dev.hydroshare.org` to ensure that code that works in one place works in all places. The only exception to this is IRODS, which is not run on the developer machines.


## 2.1 Install Git-Bash

Git-Bash provides Windows with a Git command-line ready shell that can perform linux like command calls on the Windows platform. By default Git-Bash will install with Windows style line termination `\r\n`, because we are sharing folders with a linux instance running on VirtualBox, we will want to change this to be Unix style line termination, `\n`.

Choose "**Checkout as-is, commit Unix-style line endings**"

![GitBashSetup](images/GitBashSetup.PNG)


## 2.2 Install PyCharm

Follow the linked install directions for the Windows platform.

## 2.3 Install Boot2Docker

Follow the linked install directions for the Windows platform.

#3. Getting up and running


## 3.1 Start with a fresh clone of the hydroshare repository

Not knowing what kind of line endings you currently have in your repository, it is easiest to start fresh and re-clone the hydroshare2 repository to some location on your local machine.

From the **_Git-Bash_** terminal `git clone https://github.com/hydroshare/hydroshare2.git`

This will be the directory you use as your Host Share when sharing a folder within VirtualBox. This directory will be referred to as your **LocalHydroshare** directory.

Keep this Git-Bash window open as all of the Git related calls performed on the hydroshare2 repository, or any of the submodule repositories, will be performed from here. This will be referred to as the **_Git-Bash_** terminal henceforth.

## Use linked image as base boot2docker iso

The [linked](http://people.renci.org/~stealey/boot2docker/boot2docker.iso) `boot2docker.iso` image has VirtualBox Guest Additions and fig already built into it. It was created using VirtualBox version 4.3.16 on a Windows 8.1 installation.

You can check the version of your installed VirtualBox using `VBoxManage --version` from the **_Git-Bash_** prompt. If you're not using 4.3.16, please [update](https://www.virtualbox.org/wiki/Downloads).

1. Power off boot2docker-vm if it is currently running. You can do this from the VirtualBox application itself. If the boot2docker-vm is running, right-click on it and choose **Close > Power Off.**
1. Backup your original image and use the included image in its place. (using **_Git-Bash_** from this Git directory)

	```
	$ mv $HOME/.boot2docker/boot2docker.iso $HOME/.boot2docker/boot2docker.iso.backup
	$ cp boot2docker.iso $HOME/.boot2docker/boot2docker.iso
	```
	
1. Configure the shared folder to be your local copy of the Github hydroshare2 directory. This is done from the VirtualBox application itself. 
	1. Choose **Settings > Shared Folders**. Settings will pop-up as new interface where the *Shared Folders* optionis available.
	1. From the **Machine Folders** panel choose to add a new location. This is typically appears as a **+** icon on the right side of the panel.
	1. In the **Folder Path** dialog navigate to the **LocalHydroshare** directory that you just cloned from Github.
	1. In the **Folder Name** dialog set the name to be **hydroshare**
	1. Check the **Auto-Mount** check box and click OK.
	
You should now be ready to launch the new boot2docker-vm from the **boot2docker Start** application on the Desktop.

## boot2docker Start

Once the boot2docker start application finishes launching you should have a new Git-Bash terminal window that opened with some start up dialogue. The majority of command-line code will be issued from this window in one form or another and I'll refer to it as the **_boot2docker_** terminal henceforth.

You may want to cleanup your docker environment if you've done a variety of previous installs and builds using docker. It will ensure that what is built here is starting from scratch.

Check for the presence of existing docker images and containers from the **_boot2docker_** Terminal. The list that gets generated could be quite long.
	
	$ docker images
	
If you'd like to start fresh you can remove all of the existing containers and images by using the following commands from the **_boot2docker_** Terminal.
	
	$ # Delete all containers
	$ docker rm $(docker ps -a -q)
	$ # Delete all images
	$ docker rmi $(docker images -q)

It is possible that some hydroshare related containers still remain after executing the above calls. This is not an issue as they will be re-built a little bit later.

## Preparing the LocalHydroshare directory

### Sharing folders between host and guest

From the Docker Terminal make sure you are in the `/home/docker` directory and issue an `ls` call. If you see the **hydroshare** directory, make sure it contains the code base. It is possible that nothing is returned and we'll need to link the LocalHydroshare directory to the boot2docker-vm instance from within the Docker Terminal.

This is done by first creating a hydroshare directory, and then linking it to LocalHydroshare folder.

	$ # make sure you are in the home directory
	$ cd /home/docker
	$ # create a dirctory called hydroshare and prep it for linking
	$ mkdir hydroshare
	$ sudo chmod 777 hydroshare/
	$ # mount the LocalHydroshare directory to the vm
	$ sudo mount -t vboxsf hydroshare /home/docker/hydroshare
	
If all has gone well you should see a new hydroshare directory in your vm with the same contents as the LocalHydroshare directory.


## Build base hydroshare container

The rest of this tutorial will be performed by executing commands from the **_boot2docker_** terminal

This is a Docker container that encapsulates all dependencies of Hydroshare in terms of the Linux environment and the Python environment.  It is separate from the main Hydroshare Dockerfile because it can take up to an hour to build and this makes it so that the build step only has to happen once.  In `hs_docker_base` there is a `requirements.txt` file and a `Dockerfile`.  The `Dockerfile` contains the build instructions.  The requirements.txt contains all required Python packages.  If you add a Python package you must update `requirements.txt` and rebuild by following this step.  If you add a system package (through apt-get or by downloading and building a package) you must edit `Dockerfile` and rebuild by following this step.

    $ cd hs_docker_base
    $ docker build .

    ...
    Successfully built 8d1e1ea2c2d6
	
	$ docker tag 8d1e1ea2c2d6 hs_base

For the last command, you **must** "tag" the image built by the Dockerfile `hs_base`.  This step registers the dependency in Docker's catalogue.  Without it, your `fig build` statement in the next section will fail with a 404 error.  To tag the image, copy the hexadecimal image ID from the line that starts with `Succesfully built` and paste it into the terminal window as the first argument of `docker tag` as seen above.

## Build Hydroshare

Move up one directory (the main hydroshare directory) and use fig to launch the necessary Docker containers to run HydroShare locally.

    $ cd ..
    $ fig up
    
    ...
    
There should be a lot of output as postgis, redis, worker and hydroshare build and launch. If successful you should see a *Page Not Found (404)* message when checking [http://192.168.59.103:8000](http://192.168.59.103:8000). This means everything built and is running properly, but the database is not yet resotored. Issuing a `Ctrl c` in the Docker Terminal should cause the containers to gracefully stop. You can then issue the `fig up` call with associated log files.
    
    ^CGracefully stopping... (press Ctrl+C again to force)
	Stopping hydroshare2_worker_1...
	Stopping hydroshare2_hydroshare_1...
	Stopping hydroshare2_postgis_1...
	Stopping hydroshare2_redis_1...

	Aborting.
    $ fig up > hydroshare.log 2> hydroshare.err &

Note that because of database configurations, it's likely that **you will get an error the first time** you type `fig up`.  This is expected and okay.  If it does occur, simply type `fig start` after it errors out and it will run fine.

The status of your Docker containers managed by fig can be checked by issuing a `fig ps` call. If all containers are successfully running you should see their state as being **"Up"** in the output.

	$ fig ps
	          Name                        Command               State              Ports             
	------------------------------------------------------------------------------------------------
	hydroshare2_redis_1        redis-server /etc/redis/re ...   Up      6379/tcp                     
	hydroshare2_postgis_1      /var/lib/postgresql/postgr ...   Up      5432/tcp                     
	hydroshare2_hydroshare_1   /bin/bash init                   Up      8000->8000/tcp, 1338->22/tcp 
	hydroshare2_worker_1       celery worker -A hydroshare -E   Up                                   
	$ 

Note that you can check on _hydroshare.log_ and _hydroshare.err_ for informational output and errors.

## Restore development database

In the hydroshare2 repository are two files for boostrapping your database and hydroshare environment.  They create a new environment.  **The root account for the created environment is** username **root**, password **docker**.

    $ sh restore-db hydroshare-development-db.sql
    $ sh restore-media hydroshare-development-media.tar.gz

It is possible that the `restore-media` command will prompt you for a password, if it does, the password is **docker**.

## Check your work

You should now be able to go to [http://192.168.59.103:8000](http://192.168.59.103:8000) and see a Hydroshare welcome screen.

**Important note**: to make all setup above persistent in the boot2docker VM and disk image, make sure you run the command "boot2docker save" before powering off boot2docker VM via "boot2docker down" command. 

	$ boot2docker save
	$ boot2docker status
	saved
	$ boot2docker down

To run "boot2docker save" and "boot2docker down" commands, open a separate command prompt window and type in these boot2docker commands. Also note that you have to run "boot2docker save" command EACH TIME before you power off boot2docker VM even though you saved it before. This might be a bug with boot2docker software, but the workaround is to run "boot2docker save" each time before you power it off, otherwise, all directories and set up could be gone when you again launch from boot2docker. 

## Create super user for use within local hydroshare

It is likely that you will want to create a super user for your new local development hydroshare. You can do this using the `python manage.py createsuperuser` that gets spawned in a new container. In this example I create a user named **water**.

	$ fig run --rm hydroshare python manage.py createsuperuser
	/usr/local/lib/python2.7/dist-packages/mezzanine/utils/conf.py:59: UserWarning: TIME_ZONE setting is not set, using closest match: None
	  warn("TIME_ZONE setting is not set, using closest match: %s" % tz)
	Username: water
	Email address: water@example.com
	Password: 
	Password (again): 
	Superuser created successfully.
	Removing hydroshare2_hydroshare_run_1...


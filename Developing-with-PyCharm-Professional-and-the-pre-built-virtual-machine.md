This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide)

### Requirements
* [PyCharm](http://www.jetbrains.com/pycharm/download/)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* Git 
* If you are using an IDE you need a client which supports submodules (such as [Sourcetree]( http://www.sourcetreeapp.com/))

### Table of Contents
* [1. Set up the Virtual Machine](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#wiki-1-set-up-virtual-machine)
    * 1.1 Download the Virtual Machine
    * 1.2 Add the Machine to VirutalBox
    * 1.3 Connect to vagrant host
    * 1.4 Start hydroshare-dev docker container
    * 1.5 Finish starting hydroshare-dev container
    * 1.6 Change the site record
    * 1.7 Test your local machine's connection to hydroshare-dev
    * 1.8 Get a local copy of hydroshare2 from GitHub
    * 1.9 Using SourceTree
* [2. Start Developing with PyCharm](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#wiki-2-start-developing-with-pycharm)
    * 2.1 Configure Source Control
    * 2.2 Configure Git project roots
    * 2.3 Configure Deployment
    * 2.4 Configure remote interpreter
    * 2.5 Push hydroshare to your container FIXME Untested
* [3. Common Tasks](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#3-common-tasks)
    * 3.1 Using sync-migrations
* [4. Customizing Hydroshare with an Extension](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#wiki-4-customizing-hydroshare-with-an-extension)
* [5. Troubleshooting and References](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#wiki-5-troubleshooting-and-references)
    * 5.1 Errors
    * 5.2 Passwords

## 1. Set up Virtual Machine

### 1.1 Download the virtual machine

Get the [virtual machine](http://jeff-desktop.europa.renci.org/~jeffersonheard/hydroshare-dev-vm.zip).

### 1.2 Add the machine to VirtualBox

The virtual machine file available at the link above may change, and the snapshot history and datestamp may change if there are updates to the machine. This is not important and will not affect you loading the machine into VirtualBox.  Select the VirtualBox image similar to `hydroshare_docker_vm_######` and add it to VirtualBox, as pictured below:

_Image 1:_

![add machine1](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm1.png)

Start the hydroshare_docker VM in VirtualBox, as pictured below:

_Image 2:_

![add machine2](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm2.png)

### 1.3 Connect to the vagrant host

The VM should now be running in a terminal on your desktop.  You can safely minimize the VM, as you will no longer need to connect to it directly.  Instead, open up a new terminal and SSH to it: 

    localhost ~$ ssh vagrant@localhost –p 2222
    localhost ~$ password vagrant

You should now be faced with a prompt like this, pictured in img. 3:
    
    vagrant@precise64:~$ docker ps

_Image 3:_

![connect to host](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm3.png)

**From now on, you will need to pay careful attention to the characters before the `$`.** This will show you which virtual machine you should be entering commands on. For example, in the above image, the information you are looking for is the text `vagrant@precise64`.  Working with multiple virtual machines on a single desktop can be confusing, so please check and double check this information in every step.

### 1.4 Start hydroshare-dev docker container

In this step, you will check containers and start webserver in hydroshare-dev docker container.

The redis, postgres and hydroshare containers should be already be running in the VM. However, the webserver in the hydroshare-dev container needs to be started.

To verify that all is running as expected: 

    vagrant@precise64:~$ docker ps

    CONTAINER ID	IMAGE						COMMAND			CREATED		STATUS		PORTS					NAMES
    e275e0d724c0	dev.hydrohsare.org:5999/hydroshare:latest 	/bin/bash		12 days ago	Up 6 days	0.0.0.0:1337-22/tcp, 0.0.0.0:80-80/tcp	hydroshare-dev
    543ed21ddd84	dev.hydroshare.org:5999/postgis:latest		/start.sh		12 days ago	Up 6 days	5432/tcp				hydroshare-dev/postgis,hydroshare-postgis
    be966c197c48	dev.hydroshare.org:5999/redis:latest		/bin/sh -c /usr/bin	12 days ago	Up 6 days 	6379/tcp				hydroshare-dev/redis,hydroshare-redis

This should be (roughly) what you see when you enter `docker ps`.  The important bits are under the `IMAGE` header and the `NAMES` header.  Those should be exactly as you see above.  

### 1.5 Finish starting hydroshare-dev container

Now that you've verified that everything is running, you will want to attach to the hydroshare container and get the webserver going.

    vagrant@precise64:~$ docker attach hydroshare-dev     # this is like Docker's own form of screen or SSH, you will be connected to a terminal
    (hit return again, as docker will appear to hang now)

    root@275e0d724c0:~$ cd /home/docker/hydroshare        # NOTE the new prompt!  This means you're in the hydroshare-dev container.
    root@275e0d724c0:~$ env | grep _ >> /etc/environment  # This takes the current environment and makes it exist for later SSH connections to the machine.  Do this.  You will need it later.
    root@275e0d724c0:~$ ./init                            # This starts the webserver and synchs the database

You should see some console messages, ending with:

    *** uWSGI is running in multiple interpreter mode ***
    spawned uWSGI master process (pid: 225)
    spawned uWSGI worker 1 (...)
    spawned uWSGI worker 2 (...)
    spawned uWSGI worker 3 (...)
    spawned uWSGI worker 4 (...)

If you see this, then the webserver is now working.  It is bound to your local host machine at :8001.  

In a browser on your own desktop (not the VM or anywhere else), you should now surf to `http://localhost:8001`.

### 1.6 Change the site record

* Login to the admin interface on http://localhost:8001/admin (username: hs password: water) 
* Click "sites" in the header
* Click the first record `127.0.0.1:8000`
* Edit it to be `localhost:8001`
* Click "save"

### 1.7 Test your local machine's connection to hydroshare-dev

This is on your own local host.  Open a new, clean terminal and ssh to the container with this command:

    localhost ~$ ssh docker@localhost -p 1338

The password is `docker`.  If this doesn't work, email jeff@renci.org, as PyCharm depends on an SSH connection.

### 1.8 Get a local copy of hydroshare2 from GitHub

Do not use the PyCharm Tools to pull from GitHub as these do not currently support submodules, which Hydroshare makes heavy use of. Use the command line or SourceTree instead.

Using command line on your local host (not the VM, not the Hydroshare container).  Open a fresh terminal, `cd` to the directory where you want your source tree to live, and:

    localhost ~$ git clone https://github.com/hydroshare/hydroshare2.git hydroshare
    localhost ~$ git submodule init
    localhost ~$ git submodule update

### 1.9 Using SourceTree _(this section is untested)_:
_Image 4:_

![sourcetree screencap](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm4.png)

First, add submodules:

_Image 5:_

![sourcetree submodules](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm5.png)

Then, open each submodule:

_Image 6:_

![open each submodule](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm6.png)

Final state (will change as new modules are added):

_Image 7:_

![sourcetree final state](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm7.png)

## 2. Start Developing with PyCharm

### 2.1 Configure Source Control

In the File menu, click `Open directory` and select the hydroshare repository you just cloned in the section **1.8 Get a local copy of hydroshare2 from GitHub**.  In the example pictured in img. 8 it is `hydroshare_pycharm`, but for you it is just where-ever you cloned hydroshare2.

_Image 8:_

![open directory](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm8.png)

### 2.2 Configure Git project roots

At this point, PyCharm knows where your code is, but it doesn't know about GitHub.  To correct that, you will want to register your Git roots with PyCharm.  Open the "Settings" menu.  It may be called "Preferences" on a Mac.  If the screen that comes up doesn't look like the img. 9 image below when you open it, click the "Version Control" item from the menu on the left hand and click "add root" on all the red boxes. This process is pictured in img. 10.

_Image 9:_

![configure roots](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm9.png)

_Image 10:_

![configure roots](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm10.png)

### 2.3 Configure Deployment

This section helps you configure PyCharm so that the code you develop in the IDE gets deployed to the container running on your virtual machine when you want to test it.  All the steps in this subsection (steps **a** through **e**) require that you are in the "Settings" (or "Preferences" on some systems) dialog. 

#### (a.) Open the Settings dialog. Pick the item _Project > Deployment > Options_, and follow the instructions below:

* Check “Create empty directories”
* Check “Delete target items when source ones do not exist”
* Switch “Upload changed files automatically to the default server” to “Always”  (pictured in img. 11)
* Click "Apply"

_Image 11:_

![general settings](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm11.png)

#### (b.) Add a server for Deployment:

* Pick the item _Project > Deployment_.
* Click the `+` sign to add a new Deployment target.  The dialog pictures in img. 12 below will show up.  
* Give it a name (the one we used is appropriate. 
* Pick the SFTP protocol if it is not already picked.
* Click OK. 

_Image 12:_

![server for deployment](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm12.png)

#### (c.) Edit the connection settings:
 
* Click the `Connection` tab in the dialog.
* Make sure that the settings look exactly like they do in img. 13 below (the password should be "docker"):
* Click "Test SFTP Connection" to make sure that settings work.  If they do not, send a support request to hydroshare-dev
* Click "Apply"

_Image 13:_

![completed connection settings](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm13.png)

#### (d.) Setup the deployment mapping:

* Pick the item _Project > Deployment_.
* Click the `Mappings` tab in the dialog.
* Set the local path to the path were you cloned Hydroshare2
* Set the deployment path on the server to `/hydroshare`
* Set the web path on the server to `/`
* Click "Apply"

The results should look like this:

_Image 14:_

![completed mapping setting](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm14.png)

#### (e.) Enable Django support. 

* Pick the item _Django Support_
* Enable Django support by checking on the box.

### 2.4 Configure remote interpreter

This section helps you configure PyCharm so that you can run and test code in an interpreter and so that PyCharm can syntax check and perform autocompletion using the code and libraries in the container.  All the steps in this subsection require that you are in the Settings (or Preferences on some systems) dialog. 

* Pick the item _Project Interpreter_
* Click the `+` item to add a new interpreter (see img. 15)
* Configure it as a "remote interpreter" (see img. 16)
* Click "fill from deployment server settings" (see img. 17)
* Click the name of the deployment server you setup
* Click OK (see img. 18)

_Image 15:_

![project interpreter](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm15.png)

_Image 16:_

![project interpreter](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm16.png)

_Image 17:_

![project interpreter](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm17.png)

_Image 18:_

![project interpreter](https://raw.github.com/hydroshare/hydroshare2/master/docs/pycharm18.png)

### 2.5 Push hydroshare to your container

This section assumes you have closed the "Settings" dialog and are looking at the Hydroshare project in the main IDE window.  

Sync: Right click on hydroshare project in the sidebar and select "Synchronize Hydroshare" (this performs a two way synchronization).  To perform a one-way synchronization and replace everything on the Hydroshare container, select "Upload to localhost_1338"

## 3. Common Tasks

Explore the Tools and VCS menus.  The Tools menu contains a number of entries related to Django and Python commands. The VCS menu, specifically the Git submenu contains a lot of the commands needed to interact with GitHub and commit your code.

### 3.1 Using sync-migrations

Sync-migrations will simplify using migrations in this workflow. It will will create migrations on the server side, apply them to your database, and then copy them back into your Hydroshare project tree at the appropriate place. You should run the following script anytime you edit models.py to add or remove a database field:

    ~/hydroshare $ ./sync-migrations (app-name)

Where `app-name` is the name of the Hydroshare sub-project (hs_*) whose models.py has changed.  When you run this script, the migrations will get copied back to your app's migrations directory and you will need to add them in git yourself.

Also, when you pull the latest versions of hs_core and hydroshare2, please run sync-migrations, as the resource type has been updated to support URL shortening, and all AbstractResource subclasses will have a new field added to them via migration.  

## 4. Customizing Hydroshare with an Extension

Please see the "Working on your own Hydroshare extension" section of the [Customizing Hydroshare](https://github.com/hydroshare/hydroshare2/wiki/Customizing-Hydroshare#wiki-working-on-your-own-hydroshare-extension) document for instructions on how to add a module.

## 5. Troubleshooting and References

### 5.1 Errors

**If you find yourself accidentally in a detached HEAD state,** open a terminal window and type this:

    git checkout -b fixit1 
    git checkout master
    git pull origin master
    git merge fixit1
    git branch -d fixit1

Essentially, by entering these commands, you are saving your changes make while in a detached HEAD state to a temporary branch ("fixit1"), merging them with the master branch, and then deleting your temporary branch. You will need to complete this process in all branches that are in this state.

You can read more about detached heads in [this git-notes article](https://github.com/sitaramc/git-notes/blob/master/articles/detached-head.mkd).

**If you get a 404 or 502 error** from loading http://localhost:8001

* Switch to the terminal where you are attached to the docker container
* Hit ctrl-c to kill uwsgi
* Type `uwsgi --ini uwsgi.ini`
* If you still have a problem, type `./init`

If this still results in a problem, type:
    
    localhost ~$ git checkout .
    localhost ~$ git submodule update
    localhost ~$ ./init

### 5.2 Passwords

    localhost ~$ ssh vagrant@localhost:

* password `vagrant`

    localhost ~$ ssh docker@localhost:

* password `docker`

    admin on http://localhost:8001/admin

* username `hs`
* password `water`
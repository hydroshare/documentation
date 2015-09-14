This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide). This document includes a collection of issues developers encounter while using Docker/Hydroshare with accompanying detailed notes for how they solved those issues. It is intended to serve as a guide to assist other developers with troubleshooting. This guide is a living document and should not be considered definitive.

If you are having trouble with PyCharm Professional and/or the prebuilt virtual machine, please see the [troubleshooting section](https://github.com/hydroshare/hydroshare2/wiki/Developing-with-PyCharm-Professional-and-the-pre-built-virtual-machine#5-troubleshooting-and-references) of that article.

* 1. [Docker](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development#1-docker)
    * 1.1 [Troubleshooting Tips](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development#11-troubleshooting-tips)
    * 1.2 [Troubleshooting Cases](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development#12-troubleshooting-cases)
* 2. [Hydroshare](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development#2-hydroshare)

# 1. Docker

This section will assist with problems in getting Docker installed and working. The first section focuses on general troubleshooting tips, and the second sections presents past problems with Docker and their resolutions.

## 1.1 Troubleshooting Tips

### 1.1 (a.) Follow these steps to give development some information about your environment:

    $ docker ps
    $ docker info hydroshare-dev
    $ docker history dev.hydroshare.org:5999/hydroshare
  
### 1.1 (b.) Get internal information from the `hydroshare-dev`:

    $ docker attached hydroshare-dev
    # no information will probably show. If you hit the website, it will probably show some action 

    $ control-C
    # should show root@xxxxx:/home/docker/hydroshare

    $ cd /home/docker/hydroshare/
    # what branch are you on

    $ git status
    # where

    $ git remote -v
    # what commits have been dome

    $ git log -5
    $ ls -l
    $ cd hs_core
    $ git status
    # what branch are you on

    $ git status
    # where

    $ git remote -v
    # what commits have been dome  

### 1.1 (c.) Restart your machine:

    $ cd /home/docker/hydroshare/
    $ ./init


### 1.1 (d.) Repository not found

If the Docker repository is not running, contact [Jeff Heard](https://github.com/JeffHeard):

    docker pull dev.hydroshare.org:5999/redis
    Pulling repository dev.hydroshare.org:5999/redis
    2014/01/25 17:03:55 Repository not found

### 1.1 (e.) Issue with Docker image

To ask for a new one to get generated:

    docker run -d -name hydroshare-postgis dev.hydroshare.org:5999/postgis
    2014/01/26 00:52:40 Error: create: No command specified
    Short term solution (remove -name is it complains)

### 1.1 (f.) Refreshing the Hydroshare container

If you refresh the Hydroshare container with unsaved code, you will lose that work. For now, the recommended practice is to mount everything on a local volume with -v so that you can refresh without losing work!

### 1.1 (g.) If you edit or upload a change, and do not see it reflected:

You need to log onto the hydroshare-dev, and check to be sure that a *.pyc file (or other files) are not owned by root. You can do this by:

    localhost~$ ssh docker@localhost -p 1338
    hs-dev~$ cd /home/docker/hydroshare/(PATH)
    hs-dev~$ ls -al

If files are owned by root, you need to attach to the container, and make these changes:

    localhost~$ ssh vagrant@localhost -p 2222
    hs-dev~$ cd /home/docker/hydroshare
    hs-dev~$ chown -R docker:docker .

## 1.2 Troubleshooting Cases

### 1.2 (a.) Difficulty setting up a virtual machine in Windows

**Question:**
I'm following the [instructions](http://docs.docker.io/en/latest/installation/windows/) on setting up a virtual machine. Everything looks good until I get to the `vagrant up` command. That gives me the error in the first picture:

![First error](https://raw.github.com/hydroshare/hydroshare2/master/docs/Docker%20Fail.PNG)

I've also tried modifying the VagrantFile that `init` creates, setting:

    config.vm.box = "virtualbox"
    config.vm.box_url = "http://get.docker.io/vbox/ubuntu/12.10/quanta164_3.5.0-25.box"

(mimicking the output in the tutorial). That gives me the second error:

![Second error](https://raw.github.com/hydroshare/hydroshare2/master/docs/Docker%20Fail%202.PNG)

Any idea what the error could be? Is there a VagrantFile with preconfigured options that is supposed to be present?

**Answer:**
Pull the 'doc0.8.1' branch, or look up boot2docker.

from the docker github, [remove vagrant file](https://github.com/dotcloud/docker/pull/4281), then run:

    git clone -b doc0.8.1 https://github.com/dotcloud/docker.git 

Then use [boot2docker](https://github.com/boot2docker/boot2docker).

### 1.2 (b.) Trouble viewing a local copy of Hydroshare in your browser

**Question:**
I haven't yet been able to view my local copy of hydroshare in a browser. I've got the `hydroshare/init` going in one shell, and the docker inspect shows the IPAddress is 172.17.0.4. When I try `ssh docker@172.17.0.4` I get: `Could not chdir to home directory /home/docker: Permission denied /bin/bash: Permission denied Connection to 172.17.0.4 closed`

**Answer:**

    docker stop hydoshare-dev
    docker rm hydroshare-dev
    docker pull dev.hydroshare.org:5999/hydroshare
    docker run -t -i -name hydroshare-dev \
        -link hydroshare-redis:redis \
        -link hydroshare-postgis:postgis \
        -p 0.0.0.0:80:80 -p 0.0.0.0:1337:22 \
        dev.hydroshare.org:5999/hydroshare /bin/bash

### 1.2 (c.) Setting up PyCharm in a VirtualBox

**Question:**
In the [setting up PyCharm](https://github.com/hydroshare/hydroshare2/wiki/Using-PyCharm-to-develop-on-a-Hydroshare-instance-running-inside-VirtualBox#wiki-setting-up-virtualbox-ports) part of the tutorial, it says to consult the VirtualBox documentation on mapping ports. The best I've been able to find is [this VirtualBox documentation](https://www.virtualbox.org/manual/ch06.html#natforward).

I'm guessing I need some variation of:

    VBoxManage modifyvm "VM name" --natpf1 "guestssh,tcp,,2222,,22"

But I'm not clear enough on what's going on to modify it appropriately. 

**Answer:**
Start the VirtualBox admin (oracle VM virtualbox), and use the settings>network>port forwarding.

![example](https://raw.github.com/hydroshare/hydroshare2/master/docs/gefeaghi.png)

### 1.2 (d.) Stopping the `init` process

**Question:**
What's the appropriate way of shutting things down when I'm no longer working? (How do I stop the `init` process, or am I supposed to?)

**Answer:**
In VirtualBox:

    stop machine

or:

    vagrant halt

### 1.2 (e.) Issues with [[Add new resource types or other objects that persist in the Hydroshare database]]

**Question:**
I'm having issues right now on the [[Add new resource types or other objects that persist in the Hydroshare database]] step. After defining a new resource type, I'm getting an error when creating the database tables (schema migration)...and then the localhost refuses to load properly: 

    $root@7985dd4cf5d6:/home/docker/hydroshare# python manage.py schemamigration --auto hs.linked_resources 
    $Traceback (most recent call last):
    $  File "manage.py", line 10, in <module>
    $    from settings import PROJECT_ROOT, PROJECT_DIRNAME
    $  File "/home/docker/hydroshare/settings.py", line 352, in <module>
    $    from local_settings import *
    $  File "/home/docker/hydroshare/local_settings.py", line 13, in <module>
    $    REDIS_PORT = int( os.environ.get('REDIS_PORT', 6379) ) 
    $ValueError: invalid literal for int() with base 10: 'tcp://172.17.0.3:6379'

Any idea what this is?

**Answer:**
There should be a bash profile that in the `/home/docker` directory that sets up the environment variables correctly.  

    $ source /home/docker/.bash_profile

and then retry.  This happens because you're doing everything as root, rather than SSHing in as the "docker" user.

If there is not a bash_profile:

    $ export POSTGIS_HOST=$POSTGIS_PORT_5432_TCP_ADDR
    $ export POSTGIS_PORT=5432
    $ export REDIS_HOST=$REDIS_PORT_6379_TCP_ADDR
    $ export REDIS_PORT=6379

### 1.2 (f.) Losing work between sessions

**Question:**
I loaded an instance of HS on my virtual machine. After forking a new repository in github, I cloned this new git onto my vm under my new `/hs` directory. I worked on things for a little while and then shut off my machine. This morning when I fired things back up, I noticed that my changes were not saved on the docker HS...in fact, the `/hs` directory was no longer there. Do I have to do something special so that my changes don't get rolled back every time I power down my wm? Or did I somehow magically start up an entirely different instance of HS in docker...and that's why things are messed up?

**Answer:**
Depends on how you spun up your instance of HS in docker... When you power down your machine, it's helpful to do:

    $ docker stop hydroshare-dev
    $ docker stop hydroshare-postgis
    $ docker stop hydroshare-redis

and then when you come back up, do:

    $ docker start hydroshare-dev
    $ docker start hydroshare-postgis
    $ docker start hydroshare-redis

It's possible that if you do a:

    $ docker ps -a

That you'll see an old hydroshare-dev image of some sort in the list.  If you do a:

    $ docker start (IMAGENAME) 

You should see it pop back up in the main process list, after which you can grab that instance's "container id" and paste it into a docker attach command:

    $ docker attach (CONTAINER_ID)

and that would have your code in it that you lost.

### 1.2 (g.) Finding the hydroshare folder on your local computer

**Question:**
I can’t find out the “hydroshare” folder on my local computer from the file system but I can see the dev site on localhost. So did I install the instance on my computer correctly or not? 

I followed the steps for getting a working copy of the HydroShare.  Now I can see the dev site on my local computer and from the terminal I can also see the list of all the files in the Hydroshare folder.  

When I followed the instruction for getting a working copy of hydroshare, I type the commands to get the redis, PostGIS and hydroshare container running, it shows the following warning. I don’t know if this may be a reason for my problem.

![error message](https://raw.github.com/hydroshare/hydroshare2/master/docs/error%20finding%20hs.png)

**Answer:**
The hydroshare/ folder would only be created if you clone the git repository yourself on your local machine.  Then when you start the docker container, you have to add a -v argument to your container to mount the local volume on the container.  

    $ docker run -t -i -name hydroshare-dev \
        -link hydroshare-redis:redis \
        -link hydroshare-postgis:postgis \
        -p 0.0.0.0:80:80 -expose 22 \
        -v /home/gantian/hydroshare:/home/docker/hydroshare \dev.hydroshare.org:5999/hydroshare /bin/bash
      
That will create a container that maps your local machine's git repository to the docker container. 

### 1.2 (h.) Going quickly to the root in a new terminal

**Question:**
Suppose I start a new terminal it will show as the following:

    jamy@jamy-pc:~$

How can I go to the root as the following when I started a new terminal? 

    root@ce86facd5ba9:/home/docker/hydroshare#

At present, I use the following commands to do so:

    $ sudo docker stop hydroshare-dev
    $ sudo docker start -i hydroshare-dev
    # cd /home/docker/hydroshare

Is there any other way for me to quickly go to this root?

    root@ce86facd5ba9:/home/docker/hydroshare#

After I am under the directory for the hydroshare folder, how can I quit it and return to the normal root shown as the following?

    jamy@jamy-pc:~$

**Answer:**
You will want to leave that terminal open on your computer with "init" running.  If you make changes in a models.py file, Ctrl-C out of init and then do something like the following:

    $ source /home/docker/.bash_profile
    $ python manage.py schemamigration --auto (or --init if it's the first time) ...........
    $ python manage.py migrate ............
    $ ./init &

That will leave you with the root shell.  To leave the root shell. close the terminal.  To open the root shell again, open a terminal, sudo su, and then

    $ docker attach hydroshare-dev

Hit enter a couple of times and you should get a shell again

# 2. Hydroshare 

This section deals with troubleshooting Hydroshare.

## 2.2 Tables in the database

You can see if there are tables in the database:

    psql -U docker -h POSTGIS_HOST docker \dt

## 2.3 Is it running?

I needed to initially use lynx text web browser to see that it was running, and do some sleuthing:

    $ apt-get install lynx
    $ docker inspect my-session
    somewhere in the response

    "Ports": {
        "23/tcp": null,
        "80/tcp": [
            {
                "HostIp": "0.0.0.0",
                "HostPort": "49161"
            }
        ],
        "8000/tcp": [
            {
                "HostIp": "0.0.0.0",
                "HostPort": "49162"
            }
        ]
     }

Find the HostPort for Port 80, and see if you can connect to it:

    $ lynx localhost:49161

You should see something starting with:

    #                                                       Home | Hydroshare
    #RSS Atom (BUTTON) 
    Toggle Navigation Hydroshare

If you are running in virutalbox, then in the machine settings>network>port forwarding, you can map this port (eg 49161) to a different port (say 8001).
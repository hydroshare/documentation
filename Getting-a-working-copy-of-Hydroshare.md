This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide).

You can of course get Hydroshare through the GitHub repository. However, Hydroshare has many moving parts, and getting it up and running can be a chore.  You may just want to get started.  This guide will get you a running Hydroshare instance with the latest code as quickly as possible. 

You will need PostgreSQL client programs (psql) for this guide.
<a name="sec1"/>
# 1. Getting Docker

Docker, according to its [website](http://www.docker.io/), is "an open source project to pack, ship, and run any application as a lightweight container." 

Take a moment to review the [docker container policies](https://github.com/hydroshare/hydroshare2/wiki/Policies-&-Procedures#wiki-docker-container-policy) before beginning.

To use Docker to get started with Hydroshare, you can begin by reading the [Getting Started with Docker](http://www.docker.io/gettingstarted/) page. Additionally, Docker offers a [tutorial](http://www.docker.io/gettingstarted/#h_tutorial), [installation instructions](http://www.docker.io/gettingstarted/#h_installation), and extensive [documentation](http://docs.docker.io/en/latest/).

Docker's recommended installation path is [Ubuntu Linux](http://docs.docker.io/en/latest/installation/ubuntulinux/). Docker is supported on versions 13.04, 13.10, and 12.04 (LTS), but version 12.04 is not recommended. If you are planning on running Linux in a virtual machine, it is suggested that you use the provisioning instructions Ubuntu virtual machine for your host operating system: [Mac and Linux](http://docs.docker.io/en/latest/installation/vagrant/), [Windows](http://docs.docker.io/en/latest/installation/windows/).

Installation paths and instructions for [Arch Linux](http://docs.docker.io/en/latest/installation/archlinux/), [Gentoo](http://docs.docker.io/en/latest/installation/gentoolinux/), [Fedora](http://docs.docker.io/en/latest/installation/fedora/), [FrugalWare](http://docs.docker.io/en/latest/installation/frugalware/), and [others](http://docs.docker.io/en/latest/installation/binaries/)  are also available. On other operating systems, you will have to use a Ubuntu virtual machine in order to use Docker. Instructions to do this are available for [Mac and Linux](http://docs.docker.io/en/latest/installation/vagrant/), [Windows](http://docs.docker.io/en/latest/installation/windows/), [Amazon EC2](http://docs.docker.io/en/latest/installation/amazon/), [Rackspace](http://docs.docker.io/en/latest/installation/rackspace/), and [Google Cloud](http://docs.docker.io/en/latest/installation/google/).
<a name="sec2"/>

## 1.1 Snapshot virtual machine

**Optional:** If you've chosen to run Docker in a virtual machine whose host supports snapshots, you may wish to take a snapshot before beginning this guide. This will ensure that if installation fails, you can start at this point again.

## 1.2 GNU Screen 

**Optional:** [Screen](http://www.gnu.org/software/screen/) is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells. You can use Screen to flip quickly between different running screens. You may be interested in screen if:

* You want to run a long process but don't want to background it
* You find that one of the containers is giving to much output

Using Screen is not required, but if you are interested in using it, please see [[Using Screen to run multiple containers from one console]]. The _Using Screen_ guide replaces sections 2 and 3 in this document, so once you are finished with it, please return to section [4.  View your Hydroshare instance in your browser](#sec4).

# 2. Getting Hydroshare

## 2.1 Get the Redis container running:

    $ sudo su # you need to become root
    $ docker pull dev.hydroshare.org:5999/redis
    $ docker run -d -name hydroshare-redis dev.hydroshare.org:5999/redis

## 2.2 Get the PostGIS container running:

    $ docker pull dev.hydroshare.org:5999/postgis
    $ docker run -d -name hydroshare-postgis dev.hydroshare.org:5999/postgis

## 2.3 Create the database (only do this the first time):

    $ docker pull dev.hydroshare.org:5999/hydroshare
    $ docker run -t -i \
          -link hydroshare-redis:redis \
          -link hydroshare-postgis:postgis \
          dev.hydroshare.org:5999/hydroshare /home/docker/hydroshare/initdb

When running this last line, there may be an error as it tries to delete a database that does not exist. This is expected and not an issue.

The password for the database, when it asks, is `docker`. You may have to enter this password multiple times. Now you have a working PostGIS database running on your machine as a local container with the database that Hydroshare expects to use.  From here, you should follow this pattern, substituting in the PostGIS and Redis host in place of the ellipses:
    
<a name="sec3"/>
# 3. Get a working copy on your machine to edit

First, open a terminal and clone your own copy of the hydroshare repository:
    
    $ git clone https://github.com/hydroshare/hydroshare2 hydroshare
    $ cd hydroshare

Next you will want to synch up the database with the codebase.  

    $ sudo ./create-site

This will sync the database and prompt you to create a superuser.  If this script fails for any reason, contact JeffHeard, as this means that likely a branch has gotten out of sync or a commit needs to be rolled back.  In a new terminal, run this command from within the hydroshare repository home directory (wherever you cloned the Hydroshare git repo):

    $ sudo ./run-testserver

If all has gone well, this will setup a webserver and you can surf to your local host (http://localhost) and see the Hydroshare site.  From now on any code changes you make in your localhost will be immediately reflected within the hydroshare site.  From time to time, if you edit the database schema via changes in `models.py` in one of your applications, this will cause the database to get out of sync.  If this happens, go to the terminal where the webserver is running and hit `Ctrl+C`.  This will break you out of the webserver.  

Now if you don't know what "migrations" are and there is no "migrations" directory in you project, run:

    $ sudo ./init-migrations (MY_APP_NAME)

This will either create database migrations that get your database back in sync with the codebase or it will fail and give you a helpful message about how to change your code such that it works.  Migrations are actual files in the directory that goes with your project.  They should be added to git and committed as part of your app to GitHub.  Once you have created at least an initial migration for your project, you will need to run the following whenever your database gets out of sync.   

    $ sudo ./update-migrations

<a name="sec5"/>
# 5. How to cleanly shutdown and restart the hydroshare-dev environment

If you need to reboot your machine, the following is the safest method to shutdown and then restart the hydroshare-dev environment without losing any work:

    $ sudo ./stop-all-containers

... Reboot or whatever you need to do

    $ sudo ./start-all-containers

<a name="sec6">

# 6. Refreshing the container

If you need to refresh the hydroshare container to pull the latest changes, run:

    $ sudo ./refresh-container

from within the Hydroshare root.  If you refresh the Hydroshare container in this way, your own code changes will not be affected by the refresh.  

# 7. Troubleshooting

This is a brief summary. Detailed troubleshooting information can be found on the [Troubleshooting Hydroshare Development](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development) page.

**If you're having general problems using Docker**, or **if you're seeing errors when you try to run Docker commands**, check the Docker [documentation](http://docs.docker.io/en/latest/). The following commands may be helpful with general troubleshooting:

* To see a list of images that you have downloaded into Docker:

        $ docker images

* To see a list of all the running containers:

        $ docker ps

* To see a list of call containers, even ones that are no longer executing:

        $ docker ps -a

* To see logs on a running machine. eg to see errors in the hydroshare web service 

        $ docker logs hydroshare-dev

**If you are having trouble with section [3. Get a working copy on your machine to edit](#sec3)**, then try the following:

    $ sudo ./stop-all-containers
    $ sudo service iptables stop
    $ sudo service docker restart
    $ sudo ./start-all-containers
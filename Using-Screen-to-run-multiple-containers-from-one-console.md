**Do not begin this guide unless** you have completed section 1. Getting Docker in the [[Getting a working copy of Hydroshare]] guide. This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide).

# 1. What is Screen?

[Screen](http://www.gnu.org/software/screen/) is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells. Links to documentation for Screen can be found on [GNU Screen's website](http://www.gnu.org/doc/doc.html). You can use Screen to flip quickly between different running screens. You may be interested in screen if:

* You want to run a long process but don't want to background it
* You find that one of the containers is giving to much output

Using Screen is not required. This guide replaces sections 2 and 3 in the [[Getting a working copy of Hydroshare]] document, so please begin by following _section 1. Getting Docker_ in that guide. Once you are finished with this guide, please return to _section 4.  View your Hydroshare instance in your browser_ in [Getting a working copy of Hydroshare](https://github.com/hydroshare/hydroshare2/wiki/Getting-a-working-copy-of-Hydroshare#wiki-4-view-your-hydroshare-instance-in-your-browser).

# 2. Getting Screen:

Begin by connecting to your Docker host (VM or otherwise):

    $ sudo apt-get install screen
 
You can now run a command like the following in order to detach, and come back to it later:

    $ screen -S hydroshare

You can also return to a running machine using the Docker command:

    $ docker attach (machine name)

But Screen is a general purpose tool.

## 2.1 Get the Redis container running:

    $ sudo su # you need to become root
	$ screen -S redis
    $ docker pull dev.hydroshare.org:5999/redis
    $ docker run -d -name hydroshare-redis dev.hydroshare.org:5999/redis
	$ screen -d


## 2.2 Get the PostGIS container running:

    $ docker pull dev.hydroshare.org:5999/postgis
	$ screen -S postgis
    $ docker run -d -name hydroshare-postgis dev.hydroshare.org:5999/postgis
	$ ^Ad

## 2.3 Create the database (only do this the first time):

    $ docker pull dev.hydroshare.org:5999/hydroshare
    $ screen -S hydroshare
	$ docker run -t -i \
          -link hydroshare-redis:redis \
          -link hydroshare-postgis:postgis \
          dev.hydroshare.org:5999/hydroshare /home/docker/hydroshare/initdb

# 3. Get a working copy on your machine to edit

First, login directly to the Hydroshare development container. The first two lines will fail if the container hasn't been run before, but the third should not:

    $ docker run -t -i -name hydroshare-dev \
          -link hydroshare-redis:redis \
          -link hydroshare-postgis:postgis \
          -p 0.0.0.0:80:80 -expose 22 \
           dev.hydroshare.org:5999/hydroshare /bin/bash

That will attach you to the container:

    $ cd /home/docker/hydroshare
    $ ./init
	# (deatch from screen)
	$ ^Ad 

## 3.1 Helpful commands with Screen

* To see what screens are running:

        $ screen -ls

* To detach from a screen:

        $ ^Ad  # or
        $ screen -d

* To reattach to a screen:

        # find running screens
        $ screen -ls
        $ screen -R (name/number in list)

* To page through screens forward:

        $ ^a <space> # or 
        $ ^an

* To page through screens backwards:

        $ ^ap

# Next Steps:

Please return to _section 4.  View your Hydroshare instance in your browser_ in [Getting a working copy of Hydroshare](https://github.com/hydroshare/hydroshare2/wiki/Getting-a-working-copy-of-Hydroshare#wiki-4-view-your-hydroshare-instance-in-your-browser).
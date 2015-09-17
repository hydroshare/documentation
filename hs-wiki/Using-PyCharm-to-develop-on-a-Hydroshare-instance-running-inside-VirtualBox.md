This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide). This document is deprecated. Please see the [[Developing with PyCharm Professional and the pre-built virtual machine]] document instead.

For more information on integrating PyCharm with GitHub, see the [PyCharm Documentation](https://www.jetbrains.com/pycharm/webhelp/using-github-integration.html).

### To use this guide, you will need to:
* Be running Windows or Mac OS X
* Download [PyCharm 3 Professional](http://www.jetbrains.com/pycharm/)
* Complete the the [[Getting a working copy of Hydroshare]] guide

### If the above do not apply to you:
* If you are running and developing within a desktop version of Linux, please see [[Developing Hydroshare with PyCharm on Linux]] if you want to use PyCharm in the same environment as Hydroshare
* If you are interested in developing using a text editor such as VI or Emacs on Linux, see [[Developing for Hydroshare using a text editor]]

# Getting started
This guide will help you get PyCharm working with Hydroshare running in a container on a VirtualBox VM on your local machine.  This is a typical setup for someone using Windows or Mac OS X who wants to develop on Hydroshare.  

Start with the [[Getting a working copy of Hydroshare]] guide.  Once you have completed that, you should have a working set of containers.  This guide will help you modify those containers so that you can work with the [PyCharm IDE](http://www.jetbrains.com/pycharm/), which will help you write and deploy code for Hydroshare on the container.

The Docker containers are configured to expose certain ports on your network to enable remote access to SSH, and the HTTP server. PyCharm can use a remote python interpreter to execute (and debug) the code via the SSH port. You will need to configure VirtualBox to expose the ports. This requires changes to the `hydroshare-dev` container you setup in [[Getting a working copy of Hydroshare]].

# Setting up VirtualBox ports

Consult the VirtualBox documentation on mapping ports from the VirtualBox container to  
In the end, the map of ports exposed should look like this:

| Machine    | Service  | External port | Host port |
|------------|----------|---------------|-----------|
| host       | ssh      | 2222          | 22        |
| hydroshare | http     | 8001          | 80        |
| hydroshare | ssh      | 1338          | 1337      |

    Note: hydroshare-dev must be running  the init command to connect to hydroshare ssh service

# Setting up the IDE to use the Remote

**In this section, you will:**
* Change the Hydroshare container's ports on your VirtualBox host
* Setup the remote Python interpreter in PyCharm
* Setup deployment options in PyCharm

## Changing Hydroshare container

First, stop and remove your previous `hydroshare-dev` Docker instance. Use the following to start with a fresh instance:
              
       $ docker run -t -i -name hydroshare-dev \
          -link hydroshare-redis:redis \
          -link hydroshare-postgis:postgis \
          -p 0.0.0.0:80:80 -p 0.0.0.0:1337:22 \
           dev.hydroshare.org:5999/hydroshare /bin/bash

## Configure PyCharm's remote interpreter

Open the PyCharm Settings dialog from the menu. Set your project Python interpreter to a remote interpreter. Configure it with the following settings:

* Host: 127.0.0.1
* Port: 1338
* Username: docker
* Auth type: Password
* Password: docker
* Python interpreter path: /usr/bin/python2.7

### Steps
* Open Settings :: project :: Project Interpreter :: Project Interpreters
* Click:
   * select remote
   * set name
   * Configure Remote Interpreter

# Configure your project to deploy code to the Docker container

1. Navigate PyCharm's menu through **Settings :: Project Settings :: Deployment**
2. Use the following settings:
   * **connection:** SFTP Host=localhost
   * **connection:** Port=1338
   * **connection:** User name=docker
   * **connection:** Auth Type=password
   * **connection:** Password=docker
   * **connection:** Web Server Root URL=http://localhost:8001
   * **mappings:** local path={path of hydroshare on your workstation}
   * **mappings:** deployment path=/home/docker/hydroshare
   * **mappings:** web path on server=

# Run or debug
Starting points:

* [Pastebin Docker With Pycharm](http://pastebin.com/YjU3RzM7)
* [Jetbrains Forum Question](http://forum.jetbrains.com/thread/PyCharm-1864)
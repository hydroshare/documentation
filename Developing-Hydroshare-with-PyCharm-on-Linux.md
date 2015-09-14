## Run with Docker

`requires` PyCharm Professional  or greater.

Please begin by following the guide [[Getting a working copy of Hydroshare]], but skip section _3. Get a working copy on your machine to edit_ and instead use the following command line to start the hydroshare-dev container:
 
     docker run -t -i -name hydroshare-dev \
       -link hydroshare-redis:redis \
       -link hydroshare-postgis:postgis \
       -p 0.0.0.0:80:80 -p 127.0.0.1:1337:22 \
       dev.hydroshare.org:5999/hydroshare /bin/bash 

## Configure PyCharm
 
If you want to work with the project in PyCharm and still run it in Docker, set your project Python interpreter to a remote interpreter and configure it with the following settings:
 
    Host: 127.0.0.1
    Port: 1337
    Username: docker
    Auth type: Password
    Password: docker
    Python interpreter path: /usr/bin/python2.7
    PyCharm helpers path: /home/docker/.pycharm_helpers
 
Before starting a *Django server*, configure the host IP to `0.0.0.0:8000`.
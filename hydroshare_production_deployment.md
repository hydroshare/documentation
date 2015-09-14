# HydroShare Production Deployment

This tutorial applies to the HydroShare instances running on:

1. beta.hydroshare.org
2. alpha.hydroshare.org
3. dev.hydroshare.org
4. sandbox.hydroshare.org

For purposes of the this tutorial, the users will be referenced as:

- **[user]$** is you, the user and you should have sudo level access
- **[hydrobuild]$** is the generic hydroshare build user
- **root@\<containerID>:#** is the root user within the docker container

---

## 1.0 Backup Database and Media Files for HydroShare

SSH into `{sandbox,dev,alpha,beta}.hydroshare.org` as a user that has `sudo` level permissions on the system. Change user to become the `hydrobuild` user.

	[user]$ sudo su - hydrobuild
	[sudo] password for user:
	[hydrobuild]$
	
Navigate to the `hydroshare` directory within the `hydrobuild` account.

	[hydrobuild]$ cd ~/hydroshare/


Ensure that all containers are running as expected using `fig ps`

	[hydrobuild]$ fig ps
	           Name                         Command               State                     Ports
	----------------------------------------------------------------------------------------------------------------
	hydroshare_defaultworker_1   celery worker -A hydroshar ...   Up
	hydroshare_dockerworker_1    celery worker -A hydroshar ...   Up
	hydroshare_hydroshare_1      /bin/bash init                   Up      0.0.0.0:1338->22/tcp, 0.0.0.0:80->8000/tcp
	hydroshare_postgis_1         /var/lib/postgresql/postgr ...   Up      5432/tcp
	hydroshare_rabbitmq_1        /bin/sh -c rabbitmq-server       Up      15672/tcp, 5672/tcp
	hydroshare_redis_1           redis-server /etc/redis/re ...   Up      6379/tcp
	[hydrobuild@beta hydroshare]$

### 1.1 Check location for backup files

There should be a `backup` directory at the `/home/hydrobuild/` level. If not, create one to hold the backup files.

	[hydrobuild]$ cd ~
	[hydrobuild]$ mkdir backup
	[hydrobuild]$ ls
	--should be at leaset two directories-- backup hydroshare


### 1.2 Backup Database

From the `/home/hydrobuild/hydroshare/` directory, use the `backup-db` script to backup the database, and then copy the output file to the backup directory.

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ sh backup-db
	Removing hydroshare_hydroshare_run_1...
	[hydrobuild]$ cp pg.hydroshare-mm-dd-yy.sql ~/backup
	[hydrobuild]$ ls ~/backup/
	pg.hydroshare-mm-dd-yy.sql

### 1.3 Backup Media Files

From the `/home/hydrobuild/hydroshare/` directory, use the `backup-media-linux` script to backup the media files, and then copy the output file to the backup directory. When prompted for a password, it will be **docker**.

	[hydrobuild]$ sh backup-media-linux
	root@localhost's password:
	receiving incremental file list
	media/
	media/.cache/
	media/.cache/_cached_layers/
	media/uploads/
	media/uploads/homepage/
	media/uploads/homepage/3909000656_2e8d99d667_b.jpg
	media/uploads/homepage/3957229820_4ac8c463aa_b.jpg
	media/uploads/homepage/3958584570_58bcf926ed_b.jpg
	media/uploads/homepage/.thumbnails/
	media/uploads/homepage/.thumbnails/3909000656_2e8d99d667_b-1000x500.jpg
	media/uploads/homepage/.thumbnails/3909000656_2e8d99d667_b-60x60.jpg
	media/uploads/homepage/.thumbnails/3957229820_4ac8c463aa_b-1000x500.jpg
	media/uploads/homepage/.thumbnails/3957229820_4ac8c463aa_b-60x60.jpg
	media/uploads/homepage/.thumbnails/3958584570_58bcf926ed_b-60x60.jpg

	sent 258 bytes  received 2.37M bytes  431.13K bytes/sec
	total size is 2.37M  speedup is 1.00
	~/hydroshare ~/hydroshare
	[hydrobuild]$ cp media-hydroshare-mm-dd-yy.tar.gz ~/backup

If the new deployment is going to be a clean install, go to section **2.0 Clean Install**. Otherwise proceed to section **3.0 Rebuild Incrementally**

---

## 2.0 Clean Install

Rebuilding from scratch will involve completely removing all prior docker containers, volumes and images as well as the code used to build them. This would be considered a clean install.

### 2.1 Docker Containers

Stop and remove any containers that were issued from fig

	[hydrobuild]$ fig stop
	[hydrobuild]$ fig ps
	[hydrobuild]$ fig rm
	[hydrobuild]$ fig ps
  
Stop and remove any containers, volumes or images managed by docker

	[hydrobuild]$ docker ps -a
	[hydrobuild]$ docker stop $(docker ps -a -q)
	[hydrobuild]$ docker rm -fv $(docker ps -a -q)
	[hydrobuild]$ docker ps -a
	[hydrobuild]$ docker rmi -f $(docker images -q)
	[hydrobuild]$ docker images

### 2.2 Cleanup Environment

Completely remove the hydroshare directory and all of it's contents. At this time the shared media directory is only removable by a user with sudo level access.

As sudo level user:

	[user]$ sudo rm -rf /home/hydrobuild/hydroshare/media
	
As hydrobuild user:

	[hydrobuild]$ rm -rf ~/hydroshare

### 2.3 Source Code

Checkout a clean version of the codebase and check to make sure it is up to date.

	[hydrobuild]$ git clone https://github.com/hydroshare/hydroshare.git
	[hydrobuild]$ cd hydroshare/
	[hydrobuild]$ git status
	
By default you should be on the master branch unless you've explicitly pulled a particular branch during the `git clone` command.

**Optional:** If you are merging two or more branches with the master branch you'd also perform the following
	
	[hydrobuild]$ checkout branchToMerge
	[hydrobuild]$ git branch
	[hydrobuild]$ git pull
	[hydrobuild]$ git checkout master
	[hydrobuild]$ git merge branchToMerge
	[hydrobuild]$ git status
	
Initialize and update the submodules
	
	[hydrobuild]$ git submodule init
	[hydrobuild]$ git submodule update

Copy and/or modify any of the relevant fig, docker or settings files

**Dockerfile for hs_base**

	[hydrobuild]$ cp ~/hydroshare/hs_docker_base/Dockerfile.prod ~/hydroshare/hs_docker_base/Dockerfile
	
**Dockerfile for hydroshare**

	[hydrobuild]$ cp ~/hydroshare/Dockerfile.prod ~/hydroshare/Dockerfile
	
**fig.yml for hydroshare**

	[hydrobuild]$ cp ~/hydroshare/fig.yml.prod ~/hydroshare/fig.yml
	
**local_settings.py for hydroshare**

	[hydrobuild]$ cp ~/.hsconfig/local_settings.py ~/hydroshare/hydroshare/local_settings.py

### 2.4 Build

Both `hs_base` and `hs_base_irods` should be kept up to date as image files on [dockerhub](https://hub.docker.com). In the case of the production build, image **mjstealey/hs_base_irods** is used in the **Dockerfile for hydroshare** as referenced above.

To perform a fresh build of the **hs_base_irods**, make the fig build call using the same reference as found in the Dockerfile. For example, if the Dockerfile references mjstealey/hs_base_irods, then issue:

    [hydrobuild]$ cd ~/hydroshare/hs_docker_base
    [hydrobuild]$ fig build -t mjstealey/hs_base_irods .

Use fig to build the containers

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ fig build

### 2.5 Deploy

Since the required docker containers are all built via the `fig.yml` script, they can be launched by using fig up.

	[hydrobuild]$ fig up
	
Once all containers have successfully started up, you can drop back into the standard shell by issuing `crtl z`.

	[hydrobuild]$ fig ps
	           Name                         Command               State             Ports
	------------------------------------------------------------------------------------------------
	hydroshare_postgis_1         /var/lib/postgresql/postgr ...   Up      5432/tcp
	hydroshare_redis_1           redis-server /etc/redis/re ...   Up      6379/tcp
	hydroshare_rabbitmq_1        /bin/sh -c rabbitmq-server       Up      15672/tcp, 5672/tcp
	hydroshare_hydroshare_1      /bin/bash init                   Up      80->8000/tcp, 1338->22/tcp
	hydroshare_defaultworker_1   celery worker -A hydroshar ...   Up
	hydroshare_dockerworker_1    celery worker -A hydroshar ...   Up

### 2.6 Restore database and media

**Database**

Copy the database back up to a shared volume that is accessible from both the hydroshare container and the hydrobuild user. This should be defined in the fig.yml file under volumes: for the hydroshare container.

from **fig.yml** `- "/home/hydrobuild/hydroshare:/home/docker/hydroshare"`
 
	[hydrobuild]$ sudo cp /home/hydrobuild/backup/pg.hydroshare-mm-dd-yy.sql /home/hydrobuild/hydroshare

As hydrobuild user, launch into the hydroshare container:

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ fig stop
	[hydrobuild]$ fig run --rm hydroshare /bin/bash
	root@<containerID>:/home/docker/hydroshare#

From within the hydroshare container, drop the old database, create the new database, import the backup database and migrate.

	root@<containerID>:# cd /home/docker/hydroshare/hydroshare/static/media/
	root@<containerID>:# dropdb -U postgres -h postgis postgres
	root@<containerID>:# createdb -U postgres -h postgis postgres
	root@<containerID>:# psql -U postgres -h postgis -f $(databaseBackup)
	root@<containerID>:# cd /home/docker/hydroshare/
	root@<containerID>:# python manage.py migrate
	root@<containerID>:# python manage.py collectstatic
	root@<containerID>:# exit
	
Again launch the containers by using fig up.

	[hydrobuild]$ fig up
	
Once all containers have successfully started up, you can drop back into the standard shell by issuing `crtl z`.

**Media**

From within the hydroshare directory, copy the saved media file from ~/backup into the ~/hydroshare directory and run the restore-media-linux script.

    [hydrobuild]$ cd ~/hydroshare/
    [hydrobuild]$ cp ~/backup/media-hydroshare-mm-dd-yy.tar.gz ~/hydroshare
    [hydrobuild]$ sh restore-media-linux media-hydroshare-mm-dd-yy.tar.gz
	

---

## 3.0 Rebuild Incrementally

Rebuilding incrementally will involve only rebuilding the fig wrapped containers that have modified code associated with them. The base docker images do not necessarily need to be re-downloaded or rebuilt.

### 3.1 Source Code

Pull the most recent version of the codebase and check to make sure it is up to date.

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ git pull origin master
	[hydrobuild]$ git status
	
By default you should be on the master branch unless you've explicitly pulled a particular branch during the `git pull` command.

**Optional:** If you are merging two or more branches with the master branch you'd also perform the following
	
	[hydrobuild]$ checkout branchToMerge
	[hydrobuild]$ git branch
	[hydrobuild]$ git pull
	[hydrobuild]$ git checkout master
	[hydrobuild]$ git merge branchToMerge
	[hydrobuild]$ git status
	
Update the submodules
	
	[hydrobuild]$ git submodule update --init --recursive
	
Verify that the relevant fig, docker and/or settings files have stayed in the correct format

- ~/hydroshare/fig.yml
- ~/hydroshare/Dockerfile
- ~/hydroshare/hydroshare/local_settings.py
- ~/hydroshare/hs_docker_base/Dockerfile

### 3.2 Docker Containers

It is not necessary to stop the containers while upgrading the code, but is **best practice** to do so as it will prevent anyone from accessing the site while you are making changes to the database. It is also possible to have orphaned images or containers along the way and these should be cleaned up at the end of the building process.

	[hydrobuild]$ fig stop
	[hydrobuild]$ fig ps
	[hydrobuild]$ fig rm
	[hydrobuild]$ docker ps -a
	[hydrobuild]$ docker rm any_orphaned_containers

  
### 3.3 Build

Use fig to build the containers

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ fig build

### 3.4 Deploy

Since the required docker containers are all built via the `fig.yml` script, they can be launched by using fig up.

	[hydrobuild]$ fig up
	
Once all containers have successfully started up, you can drop back into the standard shell by issuing `crtl z`.

Clean up

	[hydrobuild]$ docker ps -a
	[hydrobuild]$ docker rm any_orphaned_containers
	[hydrobuild]$ docker images
	[hydrobuild]$ docker rmi any_untagged_images
	[hydrobuild]$ fig ps
	           Name                         Command               State             Ports
	------------------------------------------------------------------------------------------------
	hydroshare_postgis_1         /var/lib/postgresql/postgr ...   Up      5432/tcp
	hydroshare_redis_1           redis-server /etc/redis/re ...   Up      6379/tcp
	hydroshare_rabbitmq_1        /bin/sh -c rabbitmq-server       Up      15672/tcp, 5672/tcp
	hydroshare_hydroshare_1      /bin/bash init                   Up      80->8000/tcp, 1338->22/tcp
	hydroshare_defaultworker_1   celery worker -A hydroshar ...   Up
	hydroshare_dockerworker_1    celery worker -A hydroshar ...   Up
	
### 3.5 Restore database and media

**Database**

Copy the database back up to a shared volume that is accessible from both the hydroshare container and the hydrobuild user. This should be defined in the fig.yml file under volumes: for the hydroshare container.

from **fig.yml** `- "/home/hydrobuild/hydroshare:/home/docker/hydroshare"`

	[hydrobuild]$ sudo cp /home/hydrobuild/backup/pg.hydroshare-mm-dd-yy.sql /home/hydrobuild/hydroshare/pg.hydroshare-mm-dd-yy.sql

As hydrobuild user, launch into the hydroshare container:

	[hydrobuild]$ cd ~/hydroshare/
	[hydrobuild]$ fig run --rm hydroshare /bin/bash
	root@<containerID>:/home/docker/hydroshare#

From within the hydroshare container, drop the old database, create the new database, import the backup database and migrate.

	root@<containerID>:# cd /home/docker/hydroshare/hydroshare/static/media/
	root@<containerID>:# dropdb -U postgres -h postgis postgres
	root@<containerID>:# createdb -U postgres -h postgis postgres
	root@<containerID>:# psql -U postgres -h postgis -f $(databaseBackup)
	root@<containerID>:# cd /home/docker/hydroshare/
	root@<containerID>:# python manage.py migrate
	root@<containerID>:# python manage.py collectstatic
	root@<containerID>:# exit

**Media**

From within the hydroshare directory, copy the saved media file from ~/backup into the ~/hydroshare directory and run the restore-media-linux script.

    [hydrobuild]$ cd ~/hydroshare/
    [hydrobuild]$ cp ~/backup/media-hydroshare-mm-dd-yy.tar.gz ~/hydroshare
    [hydrobuild]$ sh restore-media-linux media-hydroshare-mm-dd-yy.tar.gz
    
---

## 4.0 Troubleshooting

Use dbshell to verify database contents

	[hydrobuild]$ fig run --rm hydroshare python manage.py dbshell



<!-- Notes for rendering to pdf

pandoc -V geometry:margin=1in -o output.pdf input.md

-->
This is a central location for developers to share their workflows, tips and tricks, or bug fixes.  This can be used to identify and solve a variety of roadblocks that we have been encountering.  This is **not** intended to be an official development guide, rather an informal collection of notes that will be constantly changing/updating.  All developers are invited to share their notes.  Please include your name next to your post so that others know who to contact for advice.

Note: This is not a good place to ask questions!

# Index
[Setting up the HydroShare development environment (Tony)](#Setting up the HydroShare development environment)  
[Install Hs_HydroProgram (Tony)](#Install Hs_HydroProgram)

[Getting into the Hydroshare container (Pabitra)](#Getting into the Hydroshare container)

---

# Setting up the HydroShare development environment 

*Note: these steps walk through the process of setting up hydroshare on an ubuntu virtual machine (running inside vmware fusion).  The new hydroshare source code is used (i.e. without submodules)*

**Author: Tony Castronova**

**Clone hydroshare**
```
$ cd ~/home/Documents
$ git clone https://github.com/hydroshare/hydroshare.git
$ cd hydroshare
```

**Update submodules**
*There should only be two submodules to update*
```
$ git submodule init
$ git submodule update
```

**Build the hydroshare container**
```
$ cd hs_docker_base
$ docker build .

...
Successfully built 07c3abde3387

$ docker tag 07c3abde3387 hs_base
```

**Build hydroshare**
```
$ cd ..
$ fig up 
$ ctrl-z
```

**Create superuser**
```
$ fig run --rm hydroshare python manage.py createsuperuser
```

**Make sure that all containers are running**
```
$ fig ps
          Name                         Command               State              Ports             
--------------------------------------------------------------------------------------------------
hydroshare_postgis_1         /var/lib/postgresql/postgr ...   Up      5432/tcp                     
hydroshare_redis_1           redis-server /etc/redis/re ...   Up      6379/tcp                     
hydroshare_rabbitmq_1        rabbitmq-start                   Up      15672/tcp, 5672/tcp          
hydroshare_hydroshare_1      /bin/bash init                   Up      8000->8000/tcp, 1338->22/tcp 
hydroshare_defaultworker_1   celery worker -A hydroshar ...   Up                                   
hydroshare_dockerworker_1    celery worker -A hydroshar ...   Up    
```

**Restore development environment**
```
$ ./restore-db hydroshare-development-db.sql
$ mv restore-media restore-media-linux
```

Open **restore-media-linux** and change the following line: 

```
scp -r -P 1338 . root@192.168.59.103:/home/docker/hydroshare/static
```

to:  
```
scp -r -P 1338 . root@localhost:/home/docker/hydroshare/static
```

Save and close the document.

```
$ ./restore-media-linux hydroshare-development-media.tar.gz
$ password: docker
```

**Fix database error**
Navigate to [localhost:8000/admin/pages/page](localhost:8000/admin/pages/page) 

If an error occurs: 

```
fig run --rm hydroshare python manage.py shell

In [1]: from hs_core.models import GenericResource
In [2]: GenericResource.objects.all().delete()
In [3]: exit
```
---

## Install Hs_HydroProgram

```
$ cd ~/Documents/hydroshare
$ git clone https://github.com/Castronova/hs_hydroprogram
```

**Link source folder to container**  
Add the hydroshare folder to fig.yml so that your code will be automatically uploaded to the hydroshare container.  

```
$ sed -i '20i\ \ \ \ - \"~\/Documents\/hydroshare:\/home\/docker\/hydroshare\"' fig.yml
```
<!--$ sed -i '20i\ \ \ \ - \"\/home\/tony\/Documents\/hydroshare:\/home\/docker\/hydroshare\"' fig.yml -->

**Check that the code has been added to the container**
```
$ fig kill
$ fig run --rm hydroshare python manage.py shell
$ ls
$ exit
```
**Restart the container**  
`$ fig up `

**Scheme Migration**
```
$ fig run --rm hydroshare python manage.py schemamigration --init hs_hydroprogram
$ fig run --rm hydroshare python manage.py migrate hs_hydroprogram
```

**Collect Static Files**
```
$ fig run --rm hydroshare python manage.py collectstatic
```

**Install the app**  
add app to hydroshare/settings.py
```
INSTALLED_APPS = (
    "django.contrib.admin",
    "django.contrib.auth",
    ...
    ...
    "hs_hydroprogram",
```

Include app's urls in hydroshare/urls.py
```
urlpatterns += patterns('',
    url('^hsapi/', include('hs_core.urls')),
    ...
    ...
    url('^hsapi/', include('hs_hydroprogram.urls')),
)
```

**Add rich text page for resource creation**
navigate to: [localhost:8000/admin/pages/richtextpage/add/](localhost:8000/admin/pages/richtextpage/add/)
```
title:create-hydro-program
content: anything here
url:create-hydro-program
```

# Getting into the Hydroshare container

**Author: Pabitra Dash**

The standard way to get into a container is by using the 'docker attach' command. But for some reason that does not work in the current development environment that we have. May be it's a side effect of using 'fig'. Though we can run a single command against a specific container using 'fig run', there would a need in some cases when we want to be inside the container specially when something is not working. I have tried to use the following command for going into the hydroshare container:
```
fig run --rm hydroshare /bin/bash
```
However, the above command seems to add a new container/process which you can verify using 'fig ps' after you ran the above command. So I am not sure which container I am in when I use the above command.

To minimize my docker/fig confusion, I have been using a tool called 'nsenter' for looking inside the container. You can install this tool using the following command:

```
docker run -v /usr/local/bin:/target jpetazzo/nsenter
```

In order to get into container, we need to first find the ID of the container using the following command:

```
PID=$(docker inspect --format {{.State.Pid}} <container_name_or_ID>)
```

So, to find the ID of the hydroshare container, run the following command:

```
PID=$(docker inspect --format {{.State.Pid}} hydroshare_hydroshare_1)
```
Then run the following command to be inside that container:

```
nsenter --target $PID --mount --uts --ipc --net --pid
```

You can get out of the container using:

```
exit
```

# Migration Issues
**author: Tony Castronova**

After making changes to the model.py of an existing app:

```
fig run --rm hydroshare python manage.py schemamigration --init hs_hydroprogram
fig run --rm hydroshare python manage.py migrate hs_hydroprogram
``` 

results in an error:

```
...
The error was: relation "hs_hydroprogram_hydroprogramresource" already exists
...
```

To fix this, add --fake to run the initial migration as a fake:
[Stack Overflow Question](http://stackoverflow.com/questions/3090648/django-south-table-already-exists)
 
```
fig run --rm hydroshare python manage.py migrate hs_hydroprogram --fake
```

---

### See the columns in the django database:

Enter dbshell mode  
`fig run -rm hydroshare python manage.py dbshell`

Show all tables  
`postgres-# \dt`

Show tables in hydroprogram  
`postgres-# \d+ hs_hydroprogram_hydroprogramresource`  

Last resort.  Drop all hs_hydroprogram datatables
```
fig run -rm hydroshare python manage.py dbshell
hs_hydroprogram_hydroprogramresource,hs_hydroprogram_hydroprogramresource_edit_groups,hs_hydroprogram_hydroprogramresource_edit_users,hs_hydroprogram_hydroprogramresource_owners,hs_hydroprogram_hydroprogramresource_view_groups,hs_hydroprogram_hydroprogramresource_view_users;
```

Delete all migrations in `hs_hydroprogram/migrations`

build initial migration again:  
`fig run --rm hydroshare python manage.py schemamigration --init hs_hydroprogram`

migrate again:  
`fig run --rm hydroshare python manage.py migrate hs_hydroprogram`
# HydroShare Development Environment - PyCharm

This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare/wiki/Hydroshare-Developers'-Guide)

## Setup PyCharm

### Running PyCharm for the first time

By default PyCharm Professional is installed on the hydrodev VM.

You should enter your license key for PyCharm by doing the following.

```
$ cd ~/pycharm-3.4.1/bin/
$ ./pycharm.sh 
```

Terminal output should look something like this

```
hydro@hydrodev-VirtualBox:~$ cd ~/pycharm-3.4.1/bin/
hydro@hydrodev-VirtualBox:~/pycharm-3.4.1/bin$ ./pycharm.sh 
	Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=350m; support was removed in 8.0
	Oct 31, 2014 3:49:36 PM java.util.prefs.FileSystemPreferences$1 run
INFO: Created user preferences directory.
	Oct 31, 2014 3:49:36 PM java.util.prefs.FileSystemPreferences$6 run
WARNING: Prefs file removed in background /home/hydro/.java/.userPrefs/prefs.xml
```

You will likely not have any settings to import the first time you run PyCharm, so choose the second option and click **OK**.

![pycharm first run](images/pycharm_first_run.png)

Next you will be prompted to input your license key information into the dialogue screen. This information was given to you by the project PI, or Lead Investigator of the project.

![pycharm license](images/pycharm_license.png)

Accept the terms of the license agreement.

![pycharm license agreement](images/pycharm_license_agreement.png)

PyCharm initial configuration

![pycharm initial configuration](images/pycharm_initial_configuration.png)

PyCharm requires superuser credentials in order to install system level files

![pycharm superuser prompt](images/pycharm_superuser_prompt.png)

At this point you should have a registered version of PyCharm

![pycharm registered](images/pycharm_registered.png)


### Configure Source Control

In the File menu, click `Open directory` and select the hydroshare repository you just cloned in the section **HydroShare Development Environment - [Running Code](https://github.com/hydroshare/hydroshare/wiki/hydroshare_development_environment_runningcode)**.

![Open Directory](images/pycharm_open_directory.png)

In the example the repository directory is called `hydroshare`, but for you it is just where-ever you cloned the hydroshare repository at.

![Open hydroshare directory](images/pycharm_open_hydroshare.png)

### Configure Git project roots

At this point, PyCharm knows where your code is, but it doesn't know about GitHub.  To correct that, you will want to register your Git roots with PyCharm.

![Unregistered roots](images/pycharm_unregistered_roots.png)

Open the "Settings" menu (`File > Settings`).  It may be called "Preferences" on a Mac OS.  If the screen that comes up doesn't look like the image below when you open it, click the "Version Control" item from the menu on the left hand side.

![Configure roots](images/pycharm_version_control.png)

Now you will want to click "add root" on all the red boxes. Once complete the version control portion of settings should look as it does below.

![Configure roots - add](images/pycharm_add_roots.png)

### Configure Code Deployment

This section helps you configure PyCharm so that the code you develop in the IDE gets deployed to the container running on your virtual machine when you want to test it.  All the steps in this subsection (steps **a** through **e**) require that you are in the "Settings" (or "Preferences" on some systems) dialog. 

#### (a.) Open the Settings dialog. Pick the item _Project > Deployment > Options_, and follow the instructions below:

* Check “Create empty directories”
* Check “Delete target items when source ones do not exist”
* Switch “Upload changed files automatically to the default server” to “Always”
    * **NOTE** If you plan on setting up [PyCharm Remote Debugging](https://github.com/hydroshare/hydroshare/wiki/hydroshare_development_environment_pycharm_remote_debugging.md) then you may want set this to "Never", and control the uploads manually  
* Click "Apply"

![Deployment options](images/pycharm_deployment_options.png)

#### (b.) Add a server for Deployment:

* Pick the item _Project > Deployment_.
* Click the `+` sign to add a new Deployment target.
* Give it a name. 
* Pick the SFTP protocol if it is not already picked.
* Click "OK". 

![Add server plus](images/pycharm_add_server_plus.png)

![Add server name](images/pycharm_add_server_name.png)

#### (c.) Edit the connection settings:
 
* Click the `Connection` tab in the dialog.
* Your settings should look like this:
    * **SFTP Host**: `localhost`
    * **Port**: `1338`
    * **Root Path**: `/`
    * **Username**: `docker`
    * **Auth type**: `password`
    * **Password**: `docker`
    * **Web server root url**: `http://localhost:8000`
* Click "Test SFTP Connection" to make sure that settings work.  If they do not, send a support request to hydroshare-dev
* Click "Apply"

![Add localhost connection](images/pycharm_localhost_connection.png)

If this is the first time connecting to localhost you will likely see this  prompt.

![Test SFTP fingerprint](images/pycharm_test_sftp_fingerprint.png)

Upon completion you should see a successful conneciton message.

![Test SFTP connection](images/pycharm_test_sftp_connection.png)

#### (d.) Setup the deployment mapping:

* Pick the item _Project > Deployment_.
* Click the `Mappings` tab in the dialog.
* Set the local path to the path were you cloned the hydroshare repository
* Set the deployment path on the server to `/home/docker/hydroshare`
* Set the web path on the server to `/`
* Click "Apply"

The results should look like this:

![Add localhost mappings](images/pycharm_localhost_mappings.png)

#### (e.) Enable Django support. 

* Pick the item _Django Support_
* Enable Django support by checking on the box.

![Enable Django support](images/pycharm_django_support.png)

### Configure remote interpreter

This section helps you configure PyCharm so that you can run and test code in an interpreter and so that PyCharm can syntax check and perform autocompletion using the code and libraries in the container.  All the steps in this subsection require that you are in the Settings (or Preferences on some systems) dialog. 

* Pick the item _Project Interpreter_
* Click the **Gear** icon and choose "Add Remote" to add a new remote interpreter
* Click the "Deployment Configuration" radio button
* Click "OK"
* Click the name of the deployment server you setup (Remote Python 2.7.6 ... )
* Click "OK" at the bottom of the window

![Add remote interpreter](images/pycharm_add_remote_interpreter.png)

![Configure remote interpreter](images/pycharm_configure_remote_interpreter.png)

![Save remote interpreter](images/pycharm_saved_remote_interpreter.png)

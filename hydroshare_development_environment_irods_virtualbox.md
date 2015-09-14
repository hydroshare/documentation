# Installing iRODS 4.03 iCAT Server on Centos 6.6 VirtualBox Deskop Image #
Instructions on how to install an iRODS iCAT server on Centos version 6.6 linux on a VirtualBox image for the purposes of HydroShare iRODS development.

For most of the linux command lines listed below, it is recommend but not always required to cut-and-paste and execute these one-at-at-time rather than attempting to cut-and-paste and execute blocks of command lines all-at-once.

## Create VirtualBox Image of Centos 6.6 ##
Create a VirtualBox VM with the following:

- 16GB VDI disk (8GB VDI disk may suffice if you are only testing with small files)
- 1GB or more CPU memory
- 1 or 2 CPUs
- Bridged Adapter Networking

Download the 64-bit Centos version 6.6 DVD1 iso from here: http://mirror.lug.udel.edu/pub/centos/6.6/isos/x86_64/

Install the Centos .iso on the VirtualBox VM just created.  Use RedHat 64-bit as the type of linux.  Choose Centos *Desktop* type installation.

After Centos is installed and rebooted, add yourself to /etc/sudoers
```
su -
cd /etc
vi sudoers
```
add the following after the root ALL=(ALL) ALL line
```
your_user_name All=(ALL) ALL
```
```
:wq!  to exit out of vi
exit  to get out of root shell back into your user shell
```

Start Centos networking by selecting the network drop-down menu in the title bar at the top of the window and choosing the ethernet controller you would like to enable and use.

Do the following commands:
```
sudo su -
wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm
rpm -Uvh rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm

yum --enablerepo rpmforge install dkms

yum groupinstall "Development Tools"
yum install kernel-devel

exit
```
From the VM Devices menu select *Install Guest Additions CD Image....* If running a GUI the virtual CD image for guest additions will automount. The reason for guest additions is so you can cut-and-paste the instructions herein to the Centos VirtualBox linux window.

Reboot your VM after installing VirtualBox Guest Additions CD Image.

After reboot, you can eject the CD Image by right-clicking the CD icon on the desktop and choosing *Eject*.

##Instructions for installing v4.03 iRODS on CentOS 6.6  with Postgresql 9.3 
###Install dependencies for iRODS
```
sudo yum install unixODBC perl authd fuse-libs perl-JSON
sudo yum install openssl
sudo yum install openssl098e
```
###Misc configuration steps
Edit authd config file for xinetd.d (shown here with the editor vi):
```
sudo vi /etc/xinetd.d/auth
```
Remove the '-E' command line argument for auth, changing:
```
[ server_args = -t60 --xerror --os -E ]
to
[ server_args = -t60 --xerror --os ]
```
Save and exit out of your editor.

Set the proper runlevel for authd:
```
sudo /sbin/chkconfig --level=3 auth on
```
Restart xinetd: (**run this twice** as it seems to fail the first time)
```
sudo /etc/init.d/xinetd restart
```
Open your firewall, if necessary to let in iRODS. Add the following to your /etc/sysconfig/iptables file (before any REJECT entries in the chain)
```
sudo vi /etc/sysconfig/iptables
```
```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 5432 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 1247 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 20000:20199 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 20000:20199 -j ACCEPT
```
Restart the firewall:
```
sudo service iptables restart
```
Get the linux hostname:
```
hostname
```
Get the host IP address of the VirtualBox VM (remember that **Bridged Adapter** should have been selected as the VM networking type):
```
ifconfig
```
Write down the nnn.nnn.nnn.nnn value next to the output *inet addr:nnn.nnn.nnn.nnn*

Enter the VirtualBox VM hostname and ip address as the last entry in /etc/hosts as follows substituting your actual hostname for the hostname_from_above and your actual IP address for nnn.nnn.nnn.nnn:
```
nnn.nnn.nnn.nnn hostname_from_above hostname_from_above.yourdomain.org(or .edu or .com) 
```



###Install PostgreSQL 9.3
Run  following command to be able to install from postgresql 9.3 repository:
```
sudo rpm -ivh http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-centos93-9.3-1.noarch.rpm
```
Then list packages to make sure posgresql 9.3 is available to install (basically you should see a lot of files with postgresql93 somewhere in the file name):
```
sudo yum list | grep postgresql93
```
Install:
```
sudo yum install postgresql93 postgresql93-server  postgresql93-odbc
```
(this may install other dependencies like postgresql93-libs as well)

###Start up postgresql server and configure for iRODS:
Start postgresql and initialize database:
```
sudo /sbin/service postgresql-9.3 initdb
sudo /sbin/service postgresql-9.3 start
```
Create ICAT database and iRODS user, plus give iRODS user access permission to DB:
Become postgres user:
```
sudo su - postgres
```
Start postgresql command line interface and enter SQL commands:
```
psql
psql> CREATE USER irods WITH PASSWORD 'pickapassword';
psql> CREATE DATABASE "ICAT";
psql> GRANT ALL PRIVILEGES ON DATABASE "ICAT" TO irods;
\q
-bash-$ exit
```
**IMPORTANT** Whatever 'pickapassword' you entered above (single quotes necessary above) for psql must be the same password you enter in the irods_setup.sh script below


Finally, use the following command so postgres is automatically started every time your Centos VirtualBox VM is started:
```
chkconfig --level 2345 postgresql-9.3 on
```

###Install iRODS 4.03:
Download the following iRODS rpm packages by cutting-and-pasting each of the following into your browser one-at-a-time:
```
ftp://ftp.renci.org/pub/irods/releases/4.0.3/irods-icat-4.0.3-64bit-centos6.rpm
ftp://ftp.renci.org/pub/irods/releases/4.0.3-with-v1.4-database-plugins/irods-database-plugin-postgres93-1.4-centos6.rpm
```
Change to the Download director (probably in your home directory) where you downloaded the files above.

Install irods-icat and irods-database-plugin packages. The general format of the command looks like this:
```
rpm -i irods-icat-XXX.rpm irods-databse-plugin-NNN-XXX.rpm
```
Specifically for iRODS iCAT Server version 4.03 and iRODS database plugin for PostgreSQL 9.3, enter the following:
```
sudo rpm -i irods-icat-4.0.3-64bit-centos6.rpm irods-database-plugin-postgres93-1.4-centos6.rpm

```

If the above executes successfully, the output should look like this:


----------

Welcome to iRODS.

This installation of an iCAT server is currently incomplete and
needs a database plugin to be installed and configured before
it can be started and used.

Please consult the manual for further instructions.

----------

----------

iRODS Postgres Database Plugin installation was successful.

To configure this plugin, the following prerequisites need to be met:
 - an existing database user (to be used by the iRODS server)
 - an existing database (to be used as the iCAT catalog)
 - permissions for existing user on existing database

Then run the following setup script:
  sudo /var/lib/irods/packaging/setup_irods.sh

----------


Following the instructions in the output above, run the iRODS iCAT server setup script (type when prompted or just hit return to accept default entry; hostname for database server should be entered as *localhost* unless the DB is on a different machine):
```
sudo /var/lib/irods/packaging/setup_irods.sh
```
**IMPORTANT** Whatever 'pickapassword' you entered above for psql must be the same password you enter in the irods_setup.sh script.

This should complete with 5 steps of various output displayed in succession to your terminal, perhaps with some warnings, but no errors.  The final line of output should be "Done."  Scroll through the output to make sure there were no errors.  If errors, email support@hydroshare.org with the error and we'll try to help debug it.

You are now ready to access this VirtualBox VM iRODS iCAT Server from your development platform.

As a suggestion, before you use the VirtualBox VM iRODS iCAT Server you created for the first time, make a copy of it so you always have a version you can later copy and use that has no resources or collections populated in it.


----------

## Tips for the complete removal of an iRODS installation #

If you would like to completely remove an iRODS installation (perhaps to re-install without an upgrade option) use the following steps:
### WARNING: If you use this method ALL EXISTING iRODS DATA WILL BE LOST

#### Shut down iRODS:
```
sudo service irods stop
```

#### Remove all iRODS packages installed
(use 'sudo rpm -qa | grep rods' to see which packages are installed for CentOS)
```
For example: sudo rpm -e irods-icat-4.1.0-0.x86_64 irods-database-plugin-postgres93-1.5-0.x86_64
```

#### Verify iRODS related directories are removed and remove if they are not
```
sudo su - irods
rm -rf /var/lib/irods
rm -rf /etc/irods
rm -rf /tmp/irods
```

#### Remove old vault directory, if it is in place other than the default location in /var/lib/irods

#### Drop ICAT database:
```
$ sudo su - postgres
bash-4.1$ psql
psql (9.3.6)
Type "help" for help.

postgres=# drop database "ICAT";
DROP DATABASE
postgres=# \q
bash-4.1$
```

#### Remove iRODS system user:
```
sudo /usr/sbin/userdel irods
```
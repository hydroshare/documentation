# Removing iRODS
To remove iRODS from your linux development environment, execute the following commands:

Stop the iRODS iCAT server:
```
/var/lib/irods/iRODS/irodsctl stop
```
(note: this command may end in an error if the iRODS server isn't running or isn't installed - that's okay)

Stop the postgresql server
```
sudo /sbin/service postgresql-9.3 stop
```
(note: this command may end in an error if the postgresql server isn't running - that's okay)

Remove the iRODS directories:
```
sudo rm -rf /var/lib/irods
sudo rm -rf /tmp/irods
sudo rm -rf /etc/irods
```

Uninstall iRODS (use only the sudo command below for your flavor of linux):
```
DEB (e.g. Ubuntu): sudo dpkg --purge --force-all irods-icat irods-database-plugin-postgres
RPM (e.g. Centos): sudo yum remove irods-icat irods-database-plugin-postgres
```

Uninstall PostgreSQL (use only the sudo commands below for your flavor of linux):
```
DEB (e.g. Ubuntu): sudo dpkg --purge --force-all postgresql93 postgresql93-server  postgresql93-odbc
DEB (e.g. Ubuntu): sudo dpkg --purge --force-all postgresql postgresql-server  postgresql-odbc
RPM (e.g. Centos): sudo yum remove postgresql93 postgresql93-server  postgresql93-odbc
RPM (e.g. Centos): sudo yum remove postgresql postgresql-server  postgresql-odbc
```
(note: the above assumes iRODS is the only user of your postgresql installation.  If there are other users of postgrsql on your machine unrelated to the use of iRODS, you should **not** execute the above command since you would be deleting their data also)

Note that any of the commands above may end in an error if you are trying to uininstall iRODS after a botched install.  That's okay since the above commands are meant to purge everything related your initial iRODS installation regardless of the state it is in.



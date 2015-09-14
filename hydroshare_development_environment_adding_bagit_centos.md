# Adding Bagit to Centos 6.x
HydroShare uses the Bagit specification to the store resources in iRODS.  To add Bagit to your Centos 64-bit linux and iRODS development environment, execute the following commands:

First install EPEL (Extra Packages for Enterprise Linux) on Centos:
```
sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```

Next install pip:
```
sudo yum install -y python-pip
```

Finally, install bagit-python (make sure you have at least Python 2.6 installed):
```
sudo pip install bagit
```

Verify that bagit is installed in /usr/bin:
```
which bagit.py
```

You are now ready to type bagit.py from the linux commnand line.

See the following repo for instructions and more information:
```
https://github.com/LibraryOfCongress/bagit-python/blob/master/README.md
```




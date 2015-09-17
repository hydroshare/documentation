# HydroShare Development Environment - Setup

This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare/wiki/Hydroshare-Developers'-Guide)


### PyCharm information

PyCharm Professional is pre-installed on the hydrodev VM image.

You should enter your license key for PyCharm by doing the following.

Open a terminal 
```
$ cd ~/pycharm-4.0.4/bin/
$ ./pycharm.sh
```

Then input your key information into the dialogue screen.

![PyCharm License Dialogue](images/pycharmprofessionallicense.png)


### Git information

Add your Git information to the VM

- Omit `--global` to set the identity only in this repository.

```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

Once input, verify your information with

```
$ git config --list
```

### Optional Shared Folder with Host

**On the Host (Your platform)**

From the VirtualBox application, create a Shared Folder entry

1. Choose _Settings > Shared Folders_
1. Select _Machine Folders_ and click the _Add_ icon
1. Set _Folder Path:_ to be the directory you wish to share on the Host
1. Set _Folder Name:_ to be **_hydroshare_**
1. Check the _Auto-mount_ checkbox

**On the Guest (hydrodev)**

From `/home/hydro` perform the following

```
$ mkdir hydroshare
$ sudo chmod 777 hydroshare
$ sudo mount -t vboxsf hydroshare /home/hydro/hydroshare
```

With this done files in the hydroshare folder are accessible on the host and guest.

# NFIE - Getting Started With iRODS Command Line

This guide will get students of the National Flood Interoperability Experiment (NFIE) summer institute (https://www.cuahsi.org/NFIE) quickly up-to-speed on using basic iRODS icommands from the linux command line.  iRODS is the Integrated Rule-Oriented Data System (http://irods.org). 

## 

-----------
## Get Your Linux Account Credentials
Contact one of the following individuals to get your linux and iRODS account credentials on Azure, and information on where and how to login to Azure:
* Fernando Salas <fernando.r.salas@gmail.com>
* Marcelo Somos Valenzuela <marcelo.somos1@gmail.com>
* Kevin Smith <Kevin.Smith@tufts.edu>

-----------
## Setup Your Personal iRODS Information
Type the following after logging into linux on Azure:
```
cd
mkdir .irods
cd .irods
wget http://distribution.hydroshare.org/nfie/irods_environment.json
```
Then edit irods_environment.json using your favorite editor in linux:
```
nano irods_environment.json
or
vi irods_environment.json
```
Change the line that says "your_irods_user_name" to your iRODS user name:
```
{
    "irods_host": "nfie.hydroshare.org",
    "irods_port": 1247,
    "irods_default_resource": "nfieBASE_Resc",
    "irods_user_name": "your_irods_user_name",
    "irods_zone_name": "nfiehydroZone"
}
```
Then save and exit these changes


-----------
### Make sure your irods_environment.json file has a line that says:  "irods_default_resource": "nfieBASE_Resc",

or iRODS won't work correctly for you.

-----------

## Start Up iRODS Command Line 
Type the following at the linux command line prompt:
```
cd
iinit
```
Enter your iRODS password when prompted.  Note that your iRODS password is different than your linux password.

-----------
## Get A Test iRODS File To Experiment With
Type the following at the linux command line prompt:
```
cd
wget http://distribution.hydroshare.org/nfie/testfile.txt
```

-----------
## See If You Can iput A Test File Into iRODS

Type the following at the linux command line prompt:
```
icd
ipwd
ils
```
It should print out something like the following on your computer screen, where testuser will instead be your iRODS user name:
```
[testuser@nfie-teamtest ~]$ icd
[testuser@nfie-teamtest ~]$ ipwd
/nfiehydroZone/home/testuser
[testuser@nfie-teamtest ~]$ ils
/nfiehydroZone/home/testuser:
[testuser@nfie-teamtest ~]$ 
```

Now type the following at the linux command line prompt:
```
cd
iput testfile.txt
ils
ils -l
```

It should print out something like the following on your computer screen, where testuser will instead be your iRODS user name:

```
[testuser@nfie-teamtest ~]$ cd 
[testuser@nfie-teamtest ~]$ iput testfile.txt
[testuser@nfie-teamtest ~]$ ils
/nfiehydroZone/home/testuser:
  testfile.txt
[testuser@nfie-teamtest ~]$ ils -l
/nfiehydroZone/home/testuser:
  testuser          0 nfieBASE_Resc           93 2015-06-12.19:14 & testfile.txt
[testuser@nfie-teamtest ~]$ 
```

**Congratulations!!**  You have just put your first file into iRODS!


-----------

## Explanation Of The Previous Step
The following is the same text as above, except there are // after each of the commands to explain what they did:

Don't type this in - it's just for documentation purposes:
```
icd // This means "iRODS change directory" and changes to your iRODS home directory when icd has no arguments
ipwd // This displays your current iRODS working directory in iRODS 
ils // This lists the contents of all files in your current iRODS working directory
```
It should print out something like the following on your computer screen, where testuser will instead be your iRODS user name:
```
[testuser@nfie-teamtest ~]$ icd // Change directory to your iRODS home directory
[testuser@nfie-teamtest ~]$ ipwd // Display your current iRODS working directory
/nfiehydroZone/home/testuser
[testuser@nfie-teamtest ~]$ ils // List the contents of your current iRODS working directory
/nfiehydroZone/home/testuser:
[testuser@nfie-teamtest ~]$ 
```

Don't type this in - it's for documentation purposes:
```
cd // In linxux (not iRODS), change to your linux home directory
iput testfile.txt // Put the file "testfile.txt" from your current linux directory into your current iRODS directory
ils // List the contents of your current iRODS working directory
ils -l // List the contents of your current iRODS working directory in long format
```

-----------

## See If You Can iget A Test File From iRODS

Type the following at the linux command line prompt:
```
cd
icd
ils
imv testfile.text newtestfile.txt
ils
```
It should print out something like the following on your computer screen, where testuser will instead be your iRODS user name:
```
[testuser@nfie-teamtest ~]$ cd
[testuser@nfie-teamtest ~]$ icd
[testuser@nfie-teamtest ~]$ ils
/nfiehydroZone/home/testuser:
  testfile.txt
[testuser@nfie-teamtest ~]$ imv testfile.txt newtestfile.txt
[testuser@nfie-teamtest ~]$ ils
/nfiehydroZone/home/testuser:
  newtestfile.txt
[testuser@nfie-teamtest ~]$ 
```

The imv command above renamed the testfile.txt in iRODS to newtestfile.txt

Now type the following at the linux command line prompt:
```
iget newtestfile.txt
```
**Congratulations!!**  You have just retrieved your first file from iRODS!

-----------

## IMPORTANT: All iRODS iCommands Have A "-h" Option To Display HELP On That iCommand

Type the following at the linux command line prompt to display the HELP of various iRODS iCommands:

```
icd -h
ipwd -h
ils -h
imv -h
icp -h
imkdir -h
irm -h
```

### IN GENERAL, DON'T USE iput AND iget iCOMMAND-LINE OPTIONS - THESE OPTIONS CAN GET YOU INTO TROUBLE UNLESS YOU ARE AN EXPERIENCED iRODS USER
Stick to simple iput and iget commands without any command line options.


### IN GENERAL, BE VERY CAREFUL WHEN USING irm -r: IT WILL RECURSIVELY REMOVE ALL FILES AND IF NOT USED CAREFULLY IT WILL DELETE ALL YOUR FILES AND POSSIBLY REMOVE SOMEONE ELSE'S FILES AS WELL

-----------

## Navigate To NFIE Public Space Where All NFIE Users Can Put Files

Type the following at the linux command line prompt:
```
icd /nfiehydroZone/home/public
ipwd
```

It should display the following:

```
[testuser@nfie-teamtest ~]$ icd /nfiehydroZone/home/public
[testuser@nfie-teamtest ~]$ ipwd
/nfiehydroZone/home/public
[testuser@nfie-teamtest ~]$ 
```

### /nfiehydroZone/home/public/ is a special NFIE iRODS directory that all NFIE students and users can read from and write to (unless one changes the permissions)

-----------

## Recursively Put A Whole Model Output Linux Directory Into The Public Area On NFIE iRODS

Type the following at the linux command line prompt:
```
cd {to a location on linux where you have a directory of model output}
icd /nfiehydroZone/home/public
imkdir some_model_output_directory
icd some_model_output_directory
iput -r {name of your model ouptut directory on linux}
ils
ils -r
```

-----------
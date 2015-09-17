# HydroShare iRODS Bagit Workflow
HydroShare uses the [Bagit specification](https://github.com/LibraryOfCongress/bagit-python/blob/master/README.md) to the store HydroShare Resources in iRODS. The following is the iRODS Bagit Workflow:

 1 User creates HydroShare Resource

 2 User uploads file

 3 Django puts the following folder and contents associated with a HydroShare Resource in the HydroShare iRODS zone in bag-compliant (but non-zipped) format.  The 4 top level bag files are derived using Rule :: LibraryOfCongress :: rulegenerateBagIt.r:


```
bagit.txt
manifest-md5.txt
data/Contents/
data/Visualization/
data/resourcemap.xml
data/resourcemetadata.xml
```
iRODS maintains a flag that indicates whether a zipped bag exists:

```
iRODS_zipped_bag_exists=false
```

 4 User executes one of the following that requires a zipped bag:
  * Snapshot
  * Publish
  * Download Full

5 iRODS creates a zipped bag using ibun if necessary:
```
if iRODS_zipped_bag_exists=false 
  # Create zip file if one does not exist.
  ibun -c irods_path_plus_filename.zip HydroShare_irods_resource
  iRODS_zipped_bag_exists=true
```

 6 iRODS follows up on original request as follows
  * Snapshot.  Make a date stamped copy of the zipped bag. 
  * Publish.  Make the resource immutable 
  * Download full.  Send the zipped bag 

7 If any change is made by a user to the contents of a HydroShare Resource bag data folder, the following commands are executed:

```
iRODS_zipped_bag_exists=false
{User updates file(s) within HydroShare Resource using HydroShare Django that uses iRODS iput}
irods postprocforput is used to update checksums to be bagit-compliant
delete any non date stamped zipped bag file


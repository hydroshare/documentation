### Table of Contents:
* [1. Overview](#1-overview)
* [2. Roadmap](#2-roadmap)
    * [2.1 Up and Running](#21-up-and-running)
    * [2.2 Release 1](#22-release-1)
    * [2.3 Release 2](#23-release-2)
    * [2.4 Release 3](#24-release-3)
    * [2.5 Release 4](#25-release-4)
    * [2.6 Release 5 and Beyond](#26-release-5-and-beyond)
* [3. Acknowledgements](#3-acknowledgements)
* [4. References](#4-references)

# 1. Overview
HydroShare is an online, collaborative system being developed for the sharing of hydrologic data and models.  The goal of HydroShare is to enable scientists to easily discover and access data and models, retrieve them to their desktop or perform analyses in a distributed computing environment that may include grid, cloud or high performance computing model instances as necessary.  They will then be able to publish outcomes (data, results or models) back to the system, sharing with whom they choose and use the system as a platform for collaboration.  HydroShare will expand the data sharing capability of the CUAHSI Hydrologic Information System ([Tarboton et al., 2009](#4-references)) by broadening the classes of data accommodated, expanding capability to include the sharing of models and model components, and taking advantage of emerging social media functionality to enhance information about and collaboration around hydrologic data and models.  

# 2. Roadmap
This document outlines the present development roadmap HydroShare, listing the functionality that will be developed and its sequencing.  In January 2014 the development platform was changed from Drupal to Django and this roadmap is intended to guide the Django based development.

## 2.1 Up and Running
To get up and running with the Django development of HydroShare, the software stack and development environment needs to be specified.  The software stack is detailed in the requirements.txt file in this [Hydroshare2 repository](https://github.com/hydroshare/hydroshare2) where it is kept current and comprises the following major components (at present writing):

**Server:**
* virtualenv - portability of codebase for easy installation and development
* Django - Web framework
* Mezzanine - CMS framework

**Data manipulation and storage capability:**
* Pandas - data tables
* Scipy - Numerical analysis and visualization
* Numpy - Numerical Analysis
* NetCDF4 - Data Access
* SQLLite - Data interoperability and storage

**Utility:**
* Redis - Caching and key-value store
* Postgis - Web dev backend
* Celery - Backoffice and deferred task, workflow processing
* iPython - shell and web shell for lower level access
* South - Django database migrations
* iRODS - data storage and grid
* Geoanalytics - WMS, WFS, WCS, Spatial transformations, spatial data formats, data subsetting
* tastypie - API creation, authentication, authorization

**Client side:**
* Bootstrap
* JQuery
* Backbone
* Backbone relational
* Marionette.js
* D3js

**Under consideration for Search:**
* Django-solr
* Haystack
* PyCSW

A detailed list with components and their versions can be found in [requirements.txt](https://github.com/hydroshare/hydroshare2/blob/master/requirements.txt) on GitHub.  HydroShare is configured to be deployed using docker as three docker containers running each of:

* HydroShare website
* Postgis
* Redis

For development (or operational) purposes images for these docker containers can be established using scripts pulled from GitHub and described in the [[Hydroshare Developers' Guide]].

## 2.2 Release 1

Release 1 should support basic object sharing capability.  In HydroShare all content is held as a "Resource".  Resources represent the objects that are shared and are the basis for collaboration.  Resources may be simple datasets, collections of data sets or models.  The way that resources are represented enables the functionality of HydroShare.  For that reason the Resource Data Model, describing the information that HydroShare holds for each resource is critical.  The [Resource Data Model](http://beta.hydroshare.org/r/07de48d5fe424373a115208661f414b0/), described in documents on the HydroShare beta site, separates system and science metadata and has elements common to all resources as well as elements specific to the types of resources HydroShare will support.  The resource data model is based on the Bagit specification.  At Release 1 only functionality for the sharing of generic objects will be supported.

Functionality in release 1 will comprise a HydroShare website supporting:

* Upload and Sharing of Generic Objects
    * Create a resource 
    * Read a resource (detailed landing page) 
    * Update a resource (both metadata and file)
    * Delete a resource 
    * Comment on resources 
    * Rate a comment of a resource 
* Authentication and Account creation
    * Create a user  
    * Create a group  
    * User logs in 
    * User sets personal preferences 
* Access Control
* Tracking and reporting of system use 
* Terms of use, statement of privacy, and auxillary required pages (like Help)

The system will be designed using an architecture that separates the interface layer from the service layer, exposing as much of the functionality as possible through an application programmers interface so as to enable direct client access and interoperability with other systems.

Release 1 Architecture:

![Release 1 Architecture](https://raw.github.com/hydroshare/hydroshare2/master/docs/roadmap-img-1.png)

Design documents and use cases giving release 1 requirements are listed below.  Note that these links refer to the Alfresco document management system HydroShare is using.  These require an account on Alfresco.  While it is possible to create external links from Alfresco, the documents shared through such links are only viewable in the Alfresco viewer which has limitations.  If you need access to these documents in Alfresco, please contact David Tarboton, dtarb@usu.edu.

* [Resource Data Model](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace%3a%2f%2fSpacesStore%2f1f31e855-5d22-4feb-8fa2-e1d2bbf7be37) 
* Access Control [Policy](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/817322a6-aa58-44b0-9558-bfdc67ec74e9) and [Implementation Approach](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/f1d4c2d3-f954-4a60-8270-040d8b699b06) 
* [Web Service Application Programmers Interface specification](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/folder-details?nodeRef=workspace://SpacesStore/f01e21c7-417c-4331-8611-54b96b622a64) 
* [Use tracking functional requirements](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/b6a9a161-e536-49f0-bac5-6caab072fce5) 
* [Use case documents](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/folder-details?nodeRef=workspace://SpacesStore/df722619-c782-4822-b1c5-4c54bde37f90)  
    * Create resource use case ([UC 028](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/d3d53767-22a1-40d9-81b6-4bcc706ce711))
    * Read resource use case ([UC 033](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/e24c4b0b-6b7e-4760-96a5-2887c46baeca) and [UC 037](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/b2c00005-1806-4372-9a3c-e1a04cb6f3f4))
    * Update resource use case ([UC 038](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/bbfb02fd-b467-4785-972c-ff984f444665))
    * Delete resource use case ([UC 029](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/fea8c6bc-f549-45db-8d5b-6830f72974ac))
* [Comment and rate resource presentation](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/6eb5b779-6a0f-4378-bdc3-1c26595faee5)
* [Entity model (people and group representation) suggestions](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/document-details?nodeRef=workspace://SpacesStore/c8292185-7e86-4cad-ba75-15485035169d) 
* [User interface wireframe mock ups](https://my.alfresco.com/share/hydroshare.org/page/site/HydroShare/folder-details?nodeRef=workspace://SpacesStore/963333c7-72c0-44cf-896b-a0fe81361394) 

## 2.3 Release 2

Following the development of basic object sharing capability in Release 1, Release 2 will support additional data types to provide hydrology value added functionality to basic resource sharing.

Additional functionality will comprise:
* Support for time series resources (WaterML)
* Support for geographic features (Shapefiles)
* Support for geographic rasters (GeoTiff)
* Support for multidimensional space-time arrays (NetCDF)
* Support for Referenced CUAHSI HIS data series
* Support for model programs
* Search
* HydroDesktop uploading and downloading of resources 

Support for a resource requires:
* An agreed upon format specification for internal storage of the resource
* Uploaders to facilitate loading of the resource and translation from user supplied formats into the internal storage format
* Viewers to support visualization
* Exporters to support downloading and sub setting where appropriate

A referenced CUAHSI HIS data series resource is a link to a data series held on a HydroServer.

## 2.4 Release 3

Release 3 will add the following functionality:
* Support for composite resources
* Support for River Geometry (RiverML)
* HydroDesktop project package composite resource
* Support for additional referenced resource types
    * Geographic features from a web feature service
    * Geographic rasters from a web coverage service
    * Geographic map images from a web map service

Composite resources are collections of other resources.  More elaborate resource types, such as RiverML describing river geometry, model instances and model packages will be composite resources.

## 2.5 Release 4

Release 4 will add functionality to support additional collaboration, reproducible science and formal resource publication, including:

* Assignment of permanent external identifiers (DOI to published resources)
* Relationships between resources
* Capability to follow resources
* Capability to follow users
* Fixing or inclusion of externally referenced content to support reproducible science 
* Discussion forum

## 2.6 Release 5 and Beyond

Additional resource types:

* Sample based observations (the outcome of ODM2)
* Tabular objects
* Scripts
* Models (model code)
* Model packages (model code plus input/output files and parameters for a particular instance of a model run)
* Model components

Once there is the capability to support models and model packages, links will be built to execution environments, for model, workflow and script execution.

# 3. Acknowledgements

This work was supported by the National Science Foundation under collaborative grants OCI-1148453 and OCI-1148090 for the development of [HydroShare](http://www.hydroshare.org).  Any opinions, findings and conclusions or recommendations expressed in this material are those of the authors and do not necessarily reflect the views of the National Science Foundation.

# 4. References

* Tarboton, D. G., J. S. Horsburgh, D. R. Maidment, T. Whiteaker, I. Zaslavsky, M. Piasecki, J. Goodall, D. Valentine and T. Whitenack, (2009), "Development of a Community Hydrologic Information System," 18th World IMACS Congress and MODSIM09 International Congress on Modelling and Simulation, ed. R. S. Anderssen, R. D. Braddock and L. T. H. Newham, Modelling and Simulation Society of Australia and New Zealand and International Association for Mathematics and Computers in Simulation, July 2009, 988-994, http://www.mssanz.org.au/modsim09/C4/tarboton_C4.pdf.
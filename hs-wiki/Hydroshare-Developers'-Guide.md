The purpose of this guide is to assist python and web developers in contributing to Hydroshare. This guide contains reference materials and tutorials for the various libraries that are part of Hydroshare, as well as instructions for getting a working copy of HydroShare upon which to develop. Coding standards are also included in order to make Hydroshare look and feel like a single entity.

This document is primarily intended for developers looking to work on Hydroshare directly. Those looking to integrate other applications with Hydroshare would be better served by the [Hydroshare API documentation](https://github.com/hydroshare/hydroshare2/raw/master/docs/HydroShareWebServiceInterfaceDesign.pdf).

## Overview

HydroShare is a complex project composed from several interacting services, including: 

* A Django/python website
* An iRODS distributed file store. 
* A queue store with rabbitmq and celery, and two queue workers. 
* A Redis application cache. 

New components are constantly being added, so this list can expand quickly. 

Thus it is extremely difficult to build a deployment of HydroShare for development purposes without help. There are several scripts in the base HydroShare distribution that can help you build instances of the whole HydroShare suite as docker instances. Thus, you can have all of the interconnected components running on one machine rather than several. This is the way that most of us develop for HydroShare.  

## Where to Begin

The easiest way to begin with HydroShare development is to download VirtualBox and the VirtualBox image of HydroShare. Follow the instructions below to create a running HydroShare deployment within VirtualBox that contains all of the docker containers HydroShare needs to run (without data). 

* [HydroShare Development Environment - VirtualBox (hydrodev)](hydroshare_development_environment_virtualbox)
    * hydrodev - [Creating a running hydrodev HydroShare](hydroshare_development_environment_runningcode)
    * hydrodev - [Basic configuration for a specific developer](hydroshare_development_environment_setup)
    * hydrodev and general development - [Email server configuration](hydroshare_email_using_gmail)
    * hydrodev - [How to configure and use PyCharm](hydroshare_development_environment_pycharm)
    * hydrodev - [How to use remote PyCharm debugging](hydroshare_development_environment_pycharm_remote_debugging)
    * hydrodev - [[Developing on Linux with docker and PyCharm]]
* Deprecated information (out of date and not maintained)
    * **_(Deprecated)_** [[Developing with PyCharm Professional and the pre-built virtual machine]] 
    * **_(Deprecated)_** [[Developing on Windows or Mac with PyCharm Professional and Boot2Docker]]
    * **_(Deprecated)_** [[Developing on Mac with PyCharm Professional and Boot2Docker]]
* [Policies & Procedures](Policies-&-Procedures)
* [Getting started extending or customizing Hydroshare](Customizing-Hydroshare)
* [Troubleshooting Hydroshare Development](https://github.com/hydroshare/hydroshare2/wiki/Troubleshooting-Hydroshare-Development)

## Other helpful information 

It is extremely useful for the HydroShare developer to be familiar with: 

* Basic Django development and manage.py.
* How to interact with docker containers. 

These are beyond the scope of this wiki but there are many online resources. 

## Specialized Guides
* [Resource creation part I: add new resource types or other objects that persist in the Hydroshare database](Add-new-resource-types-or-other-objects-that-persist-in-the-Hydroshare-database)
* [Resource creation part II: Resource creation guide using the new resource creation workflow](https://github.com/hydroshare/hydroshare/wiki/Resource-creation-guide-using-the-new-resource-creation-workflow)
* [How to extend core metadata elements for a specific resource type](Extending Core Metadata Elements)
* [How to implement resource specific upload file type validation](Implement Resource Type Specific Upload File Validation)
* [How to add an API](Adding-an-API)
* [How to add web application endpoints](Adding-application-endpoints-to-the-website)
* [How to use long-running tasks](Adding-Tasks)
* [How to use automated tests](Adding-Tests)

## Using PyCharm
* [[Developing with PyCharm Professional and the pre-built virtual machine]]
* [PyCharm Professional documentation](http://www.jetbrains.com/pycharm/quickstart/)
* [[Developing Hydroshare with PyCharm on Linux]]
* [PyCharm Remote Debugging] (hydroshare_development_environment_pycharm_remote_debugging)

## Resources Lists
* [Javascript libraries available to Hydroshare apps](Javascript-libraries-available-to-Hydroshare-web-applications)
* [Python libraries available to Hydroshare](Python-libraries-available-to-Hydroshare)
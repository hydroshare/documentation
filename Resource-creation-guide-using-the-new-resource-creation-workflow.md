# Workflow guide for adding new resource types in Hydroshare #
This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare/wiki/Hydroshare-Developers'-Guide).

This guide is a supplement to the document [Add new resource types or other objects that persist in the Hydroshare database](https://github.com/hydroshare/hydroshare/wiki/Add-new-resource-types-or-other-objects-that-persist-in-the-Hydroshare-database) to leverage the new resource creation workflow recently added into Hydroshare aimed at reusing generic resource creation code as much as possible for creation of a new resource type in Hydroshare. [Raster resource type](https://github.com/hydroshare/hydroshare/tree/AGU/hs_geo_raster_resource) will be used as an example to illustrate the workflow throughout this guide.  

### Defining a new resource type and creating a Django app for the new resource type to add into Hydroshare project ###

This can be achieved by following the document [Add new resource types or other objects that persist in the Hydroshare database](https://github.com/hydroshare/hydroshare/wiki/Add-new-resource-types-or-other-objects-that-persist-in-the-Hydroshare-database). Alternatively, you can use an existing resource type app in Hydroshare project (e.g., hs_geo_raster_resource, hs_rhessys_inst_resource, etc.) as a template to copy over and modify as needed for your new resource type. For better understanding, it is recommended to read through the document [Add new resource types or other objects that persist in the Hydroshare database](https://github.com/hydroshare/hydroshare/wiki/Add-new-resource-types-or-other-objects-that-persist-in-the-Hydroshare-database) to follow along even if you use an existing resource type as a template to modify for your new resource type.   

### Define your models in `models.py` for creating tables in the database  ###
You will need to edit `models.py` to create needed tables in the database for your new resource type. Refer to `hs_geo_raster_resource/models.py` for an example. The basic code skeleton is summarized below:

		from django.contrib.gis.db import models
        from mezzanine.pages.models import Page
        from hs_core.models import AbstractResource
        from hs_core.models import resource_processor
        from mezzanine.pages.page_processors import processor_For

        class MyResourceType(Page, AbstractResource):
            pass

        processor_for(MyResourceType)(resource_processor)
		# page processor to populate resource specific metadata into my-resources template page
		@processor_for(MyResourceType)
		def resource_page(request, page):
			# return a dictionary that will be added to the template context or return any of Django's HttpResponse classes 
			
		import receivers

The `@processor_for` Mezzanine decorator function allows for your new resource type template page to pick up all the necessary variables as template context. In your myresourcetype.html template page, you should extend from pages/genericresource.html to leverage generic resource description page to minimize code duplication. Please see `hs_geo_raster_resource/templates/pages/rasterresource.html` and `hs_rhessys_inst_resource/templates/pages/instresource.html`for two examples.

The last line imports receivers.py that needs to be created as part of this new resource type app to define receivers/handlers to those signals raised from generic resource creation workflow in order to leverage the generic resource creation workflow to do any resource-specific work which will be described below.     

### Define your signal handlers in receivers.py to do resource-specific work ###

Currently, the generic resource creation workflow in Hydroshare raises the following signals:



- **pre\_describe\_resource** : raised before a resource of a specific type is ready to be described with metadata.    

- **pre\_call\_create\_resource** : raised before hydroshare.create_resource() API function is called to create a resource.

- **pre\_create\_resource** : raised before the resource is ready to be created in hydroshare.create_resource() API function.    

- **post\_create\_resource** : raised after resource has been created but before the bag is created in hydroshare.create_resource() API function.

The **pre\_call\_create\_resource** and **pre\_create\_resource** signals sound duplicate at first glance, but the former is needed to pass in the specific resource type metadata internally in Hydroshare view function to hydroshare.create_resource() API function which could be used as an external create_resource() REST API function as well, and the latter is needed in case external apps using hydroshare.create_resource() REST API function call to create a hydroshare resource can listen to this pre\_create\_resource signal as well.

A specific resource type app can listen to these signals and write signal handlers in receivers.py to do resource specific work as detailed below:

- **pre\_describe\_resource** receiver: automatic metadata extraction for the specific resource type from the uploaded file can be performed here in the receiver. This receiver returns resource-specific metadata terms as a dictionary for generic resource creation workflow to add into template contexts to be picked up by the generic resource creation page to display to users for further metadata editing. **Note that a specific resource type does not have to reuse the generic resource creation workflow if the specific resource type workflow diverges from generic resource creation workflow significantly**, e.g., reference time series resource type. In this case, the specific resource type can use their own resource creation page if so desired by returning a key pair in the context dictionary from this receiver, e.g.,  "create_resource_page_url": "pages/create-ref-time-series.html." If such key pair does not exist in the returned dictionary from this receiver, the resource creation workflow will use the default generic create-resource.html page.   

- **pre\_call\_create\_resource** receiver: metadata validation can be performed here in the receiver, and if valid, the receiver returns a dictionary of metadata terms to be passed on to hydroshare.create_resource() API function call to use when creating the resource with specific type.

- **pre\_create\_resource** receiver: metadata validation can be performed for external app creating a hydroshare resource using the REST API hydroshare.create_resource() function call.    

- **post\_create\_resource** receiver: any resource-specific work to be done after resource is created and before a bag is created can be performed here.

Refer to hs_geo_raster_resource/receivers.py for an example on how these signal receivers are used.  

### Define your Django form for metadata validation in forms.py ###
This form is optional and used for metadata validation purpose. Refer to hs_geo_raster_resource/forms.py for an example.

### Add your resource app in Hydroshare project and perform any database migration as needed ###

Last step is to add your new resource app into INSTALLED_APPS list in hydroshare/settings.py, build the project, and perform any database migration as needed as standard steps for adding any new app into a Django project.
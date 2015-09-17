This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide).

### In this guide you will need to edit:
* `hs_app/models.py`
* `hs_app/admin.py`
* `hs_app/templates/pages/myresourcetype.html`

### For more information:
* [Django Models Documentation](https://docs.djangoproject.com/en/1.6/topics/db/models/)
* [Django Signals Documentation](https://docs.djangoproject.com/en/1.6/topics/signals/)
* [GeoDjango Models Documentation](https://docs.djangoproject.com/en/1.6/ref/contrib/gis/model-api/)

# Defining a new resource type
**First, you should determine if you actually need to define a new resource type.** A different filetype is not the same thing as a different resource type. You will only need a new resource type if you have to extract metadata from the resource files in a specific way, define metadata outside the dublin core (which is not advised), or visualize/summarize the data in the landing page.

A number of Hydroshare extensions define new Resource types. Resource types define persistent objects in the database schema that are considered first-level objects in Hydroshare. All types of content inherit from the  Page model in Mezzanine. To read more about this model, please see the [Mezzanine Content Architecture](http://mezzanine.jupo.org/docs/content-architecture.html) documentation.

**Once you are sure you need to define a new resource type,** follow the steps below. This process involves a little code and a couple of maintenance steps.  

1.  First we will edit `models.py`. This will create the datatype to the database. The last line will cause the resource.html template to pick up all the necessary variables. If you are not currently extending resource.html, please consider doing so as a lot of your work will be done for you. Please see `hs_core/templates/pages/genericresource.html` for an example of this and `hs_core/templates/hydroshare/resource.html` to see what's included in this template.

        from django.contrib.gis.db import models
        from mezzanine.pages.models import Page
        from hs_core.models import AbstractResource
        from hs_core.models import resource_processor
        from mezzanine.pages.page_processors import processor_For

        class MyResourceType(Page, AbstractResource):
            pass

        processor_for(MyResource)(resource_processor)

2.  The next thing is to register this datatype with the Django admin interface.  Edit `admin.py`:

        from mezzanine.pages.admin import PageAdmin
        from django.contrib import admin
        from .models import MyResourceType

        admin.register(MyResourceType, PageAdmin)

# Creating tables in the database 
Now that we have a new resource type, we need to create the tables in the database.  

1. First, change directories to the main `hydroshare/` directory. You will then type the following, where `hs.my_hydroshare_app` is the name of your Hydroshare extension.  This will create a new directory, `hs/my_hydroshare_app/migrations` and a single file, which will update the database.

        $ python manage.py schemamigration --init hs.my_hydroshare_app
  
2. To update the database:

        $ python manage.py migrate hs.my_hydroshare_app

3. Anytime you add fields to your resource or change a field's datatype or default value, it is a good idea to run the following code to catch any new changes.  Django's migration mechanism also catches common "errors" in database changes, such as forgetting to allow NULL values in nullable fields or providing sensible defaults.

        $ python manage.py schemamigration --auto hs.my_hydroshare_app
        $ python manage.py migrate hs.my_hydroshare_app

# Super-classes

The exact order of classes in `models.py` is important.  You must first inherit from `Page` then `AbstractResource` to have everything work properly. For more information, see:

 * [Mezzanine documentation about the Page model and content architecture](http://mezzanine.jupo.org/docs/content-architecture.html) 
 * [The models.py file in the mezzanine repository](https://github.com/stephenmcd/mezzanine/blob/master/mezzanine/pages/models.py)
 * [The models.py file in the hydroshare/hs_core repository](https://github.com/hydroshare/hs_core/blob/master/models.py)

# Note on using the Admin interface with custom resources

If you try to add a content page instance for your new resource type via the Django admin site (i.e. through the Content->Pages menu), you will get the error "DoesNotExist at /admin/pages/page/, User matching query does not exist." So you cannot add a new resource type content page.  This is because the Django admin site does not call HydroShare.create_resource.  Instead, create the resource page via the HydroShare interface. 

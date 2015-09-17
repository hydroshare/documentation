This document is part of the [[Hydroshare Developers' Guide]].

Endpoints are web service URLs that perform extra functionality beyond the standard Hydroshare basics.  There are a few utilities in the Hydroshare grab-bag that will help smooth the creation of these services in a standard way.   This document will describe how to write custom webservices in a way that they will be serviced by Hydroshare

Hydroshare takes care of Create, Read, Update, List, Search, and Delete for you. Much of our boilerplate code takes care of basic cases. Other guides about creating Hydroshare resources cover this as well. Sometimes, however, the basics will not suffice, and you will have to create new endpoints for serving or editing data from within a resource.  

The basic steps covered in this guide:

* [1. Write the view](https://github.com/hydroshare/hydroshare2/wiki/Adding-application-endpoints-to-the-website#1-write-the-view). Creating a [view](https://docs.djangoproject.com/en/dev/topics/http/views/) requires following a few basic steps to normalize it for Hydroshare
* [2. Add a URL pattern to your app's `urls.py`](https://github.com/hydroshare/hydroshare2/wiki/Adding-application-endpoints-to-the-website#2-add-a-url-pattern-to-your-urlspy)
* [3. Ensure that your app's `urls.py` is included](https://github.com/hydroshare/hydroshare2/wiki/Adding-application-endpoints-to-the-website#3-ensure-your-apps-urlspy-is-included) (in the main `hydroshare/urls.py`)

# 1. Write the view

There is a plethora of documentation on creating views in Django already.  Rather than going into this, we point you at the main [Django views documentation](https://docs.djangoproject.com/en/dev/topics/http/views/) and ask you to follow the examples there to understand how to write a view.

To write a view specifically for Hydroshare, though, we recommend the following:

    from ga_resources.utils import json_or_jsonp, authorize, get_user

    def my_view_function(request, *args, **kwargs):
        resource_page = ... # YOU define something to get the resource you're operating on
        authorize(request, resource_page, view=..., edit=..., update=..., delete=...) # raise an exception if the requesting user cannot do whatever it is your webservice does
        user = get_user(request) # handles if the user is using an API key to access this or is already authenticated with the system
        
        ... do stuff ...
        normalized_results = ...
        return json_or_jsonp(request, normalized_results) # this will detect what kind of output the user is requsting and serialize it properly if it is JSON serializable. 

# 2. Add a URL pattern to your urls.py

See django documentation on URL patterns:

* [URL Dispatcher](https://docs.djangoproject.com/en/dev/topics/http/urls/)
* [Naming URL Patterns](https://docs.djangoproject.com/en/dev/topics/http/urls/#naming-url-patterns)
* [Using URL Patterns and Views](http://www.webmonkey.com/2010/02/use_url_patterns_and_views_in_django/) (external source)

# 3. Ensure your app's `urls.py` is included.

All URLs in Hydroshare should be namespaced.  The way we do this is to use your app name as the prefix for the URL. Towards the beginning of `hydroshare/urls.py` (there is documentation in the file to tell you where to add this), add your urls by including a line like this:

    url('hs_my_app/', include('hs_my_app.urls'))

        


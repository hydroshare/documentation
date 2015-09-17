This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers-Guide).

Client side programming is generally an HTML and Javascript affair.  There are a number of libraries available by default in Hydroshare.  These are in the `hydroshare/hs_core/static/js` and `hydroshare/hs_core/static/css` folders in the Hydroshare codebase.  

These are included by default in every page and do not need to be added to your page to use them:
* [Bootstrap](http://getbootstrap.com/javascript/#js-overview) - thin skin on HTML to make it prettier
* [Font Awesome](Getting Started: http://fontawesome.io/get-started/) - icons for HTML5
* [JQuery](http://api.jquery.com/) - Web page online manipulation in Javascript

For the rest of the libraries in this document, to use these libraries in your code include them in a block in your page template called `{% block extra_js %}` or `{% block extra_css %}`  like this:

#### my_hydroshare_app/templates/pages/mycontenttype.html
    {% extends "page.html" %}
    {% load staticfiles %}
    {% load pages_tags %} 

    {% block extra_js %}
        {% include "includes/hydroshare/backbone.html" %}
    {% endblock %}
The following can be added by including the `includes/hydroshare/backbone.html` template in your `extra_js` block (see above for an example).
* [Backbone](http://backbonejs.org) - Javascript data structure extensions
* [Backbone Relational](http://backbonerelational.org/) - Connect automatically to RESTful web APIs and get simple, understandable objects.
* [Backbone Marionette](http://marionettejs.com/) - Make webpages into web applications (scroll down to “Downloads and Documentation”)

Include `includes/hydroshare/datetime.html`
* [Moment](http://momentjs.com) - Rich date and time manipulations

Include `includes/hydroshare/mapping.html`
* [OpenLayers](http://trac.osgeo.org/openlayers/wiki/Documentation) - Interactive web mapping; the stuff that Google forgot in Google Maps API
  * [API documentation](http://dev.openlayers.org/releases/OpenLayers-2.13.1/doc/apidocs/files/OpenLayers-js.html)
  * [Development Examples](http://openlayers.org/dev/examples/) (very very useful)

Include `includes/hydroshare/visualization.html`
* [D3](https://github.com/mbostock/d3/wiki) - Interactive information visualization toolkit


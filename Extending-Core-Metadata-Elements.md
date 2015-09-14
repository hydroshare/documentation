**Author: Pabitra Dash and Tian Gan**

This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide). This guide describes how to extend the Dublin core metadata elements to include new metadata elements for your specific resource type. 

In this example, we will create a new resource type called "NetcdfResource". For this resource type we will have an additional metadata element called "Variable". The "Variable" metadata element will have the following 2 sub-elements:
* name
* unit
* type
* shape
* descriptive_name
* method
* missing_value

##Step-1:
In your Django app for the NetcdfResource, open the 'models.py' file and import the following modules specific to core metadata:

`from django.contrib.contenttypes import generic`

`from django.db import models`

`from mezzanine.pages.models import Page`

`from mezzanine.pages.page_processors import processor_for`

`from hs_core.models import AbstractResource`

`from hs_core.models import resource_processor, CoreMetaData, AbstractMetaDataElement`

`from django.contrib.contenttypes.models import ContentType`

`from django.core.exceptions import ObjectDoesNotExist, ValidationError`

##Step-2:
We will first create our class for the 'NetcdfResource' resource type. This class must implement the following property:
* metadata: This property needs to return the metadata container object associated with the resource. See Step-4 for details on metadata container class. The name of our metadata container class is 'NetcdfMetaData' (which we will define in Step-4). In our app's models.py file we define the 'NetcdfResource' class as shown below:

```
class NetcdfResource(Page, AbstractResource):

    @property
    def metadata(self):
        md = NetcdfMetaData()
        return self._get_metadata(md)

    def can_add(self, request):
        return AbstractResource.can_add(self, request)

    def can_change(self, request):
        return AbstractResource.can_change(self, request)

    def can_delete(self, request):
        return AbstractResource.can_delete(self, request)

    def can_view(self, request):
        return AbstractResource.can_view(self, request)

    class Meta:
            verbose_name = 'NetCDF Resource'

processor_for(NetcdfResource)(resource_processor)
```
 
##Step-3:
Create a class called 'Variable' to represent the new metadata element for your resource. This class must inherit the 'AbstractMetadataElement' class. The 'Variable' class must implement the following methods:
* create(): This function needs to implement all business logic needed to create an element of type 'Variable'
  * Rule:Variable name needs to be unique, data is required for sub-elements 'name, 'type', and 'shape'
* update(): This function needs to implement all business logic needed to update an existing element of type 'Variable'
  * Rule:Variable name needs to be unique
* remove(): This function needs to implement all business logic needed to delete an existing element of type 'Variable'
  * Rule: At lease there has to be one variable element for a given NetCDF resource

In your apps models.py file add the following code for the class 'Variable':

```
class Variable(AbstractMetaDataElement):
    VARIABLE_TYPES = (
        ('char', 'Char'),
        ('byte', 'Byte'),
        ('short', 'Short'),
        ('int', 'Int'),
        ('float', 'Float'),
        ('double', 'Double')
    )
    term = 'Variable'
    # required variable attributes
    name = models.CharField(max_length=100)
    unit = models.CharField(max_length=100)
    type = models.CharField(max_length=100, choices=VARIABLE_TYPES)
    shape = models.CharField(max_length=100)
    # optional variable attributes
    descriptive_name = models.CharField(max_length=100, blank=True)
    method = models.TextField(blank=True)
    missing_value = models.CharField(max_length=100, blank=True)

    def __unicode__(self):
        self.name

    @classmethod
    def create(cls, **kwargs):
        # Check the required attributes and create new variable meta instance
        if 'name' in kwargs:
            # check if the variable metadata already exists
            metadata_obj = kwargs['content_object']
            metadata_type = ContentType.objects.get_for_model(metadata_obj)
            variable = Variable.objects.filter(name__iexact=kwargs['name'], object_id=metadata_obj.id,
                                               content_type=metadata_type).first()
            if variable:
                raise ValidationError('Variable name:%s already exists' % kwargs['name'])
        else:
            raise ValidationError("Name of variable is missing.")

        if not 'unit' in kwargs:
            raise ValidationError("Variable unit is missing.")

        if 'type' in kwargs:
            if not kwargs['type'] in ['char', 'byte', 'short', 'int', 'float', 'double']:
                raise ValidationError('Invalid variable type:%s' % kwargs['type'])
        else:
            raise ValidationError("Variable type is missing.")

        if not 'shape' in kwargs:
            raise ValidationError("Variable shape is missing.")

        variable = Variable.objects.create(name=kwargs['name'], unit=kwargs['unit'], type=kwargs['type'],
                                            shape=kwargs['shape'], content_object=metadata_obj)

        # check if the optional attributes and save them to the variable metadata
        for key, value in kwargs.iteritems():
                if key in ('descriptive_name', 'method', 'missing_value'):
                    setattr(variable, key, value)

                variable.save()

        return variable


    @classmethod
    def update(cls, element_id, **kwargs):
        variable = Variable.objects.get(id=element_id)
        if variable:
            if 'name' in kwargs:
                if variable.name != kwargs['name']:
                # check this new name not already exists
                    if Variable.objects.filter(name_iexact=kwargs['name'], object_id=variable.object_id,
                                         content_type__pk=variable.content_type.id).count()> 0:
                        raise ValidationError('Variable name:%s already exists.' % kwargs['name'])

                variable.name = kwargs['name']

            for key, value in kwargs.iteritems():
                if key in ('unit', 'type', 'shape', 'descriptive_name', 'method', 'missing_value'):
                    setattr(variable, key, value)

            variable.save()
        else:
            raise ObjectDoesNotExist("No variable element was found for the provided id:%s" % kwargs['id'])

    @classmethod
    def remove(cls, element_id):
        variable = Variable.objects.get(id=element_id)
        if variable:
            # make sure we are not deleting all variables of a netcdf resource
            if Variable.objects.filter(object_id=variable.object_id, content_type__pk=variable.content_type.id).count()== 1:
                raise ValidationError("The only variable of the resource can't be deleted.")
            variable.delete()
        else:
            raise ObjectDoesNotExist("No variable element was found for id:%d." % element_id)
```
**Note**: If your specific resource type needs additional metadata elements, you have to create a class for each of these elements similar to how we have created the 'Variable' class above.


##Step-4:
Now we need to create a metadata container class which must inherit from the 'CoreMetaData' container class. The purpose of a metadata container class is to group a number of metadata element class types. By inheriting from the 'CoreMetaData' container class, our new container class will automatically have association with all the dublin core metadata element types. A resource of a specific type is associated with a metadata container object of a given type. By having a relationship between a specific resource type and a specific metadata container type, we define what metadata elements are associated with a given resource type. For example, the 'GenericResource' resource type has been associated with the 'CoreMetaData' container class in hs_core.

The name of the metadata container class for our resource type 'NetcdfResource' is 'NetcdfMetaData'. In this class we need to define generic relations to each of the additional metadata element class types that we want to be part of our 'NetcdfResource' resource type. For our current example we have only one additional metadata element called 'Variable'. This class must implement the following methods/properties:
* get_supported_element_names(): This method needs to return a list of metadata element class names that are associated with this container class.
* delete_all_elements(): This method needs to delete all element type objects associated with this container object.
* get_xml(): This method needs to return an xml string that represents the science metadata for the associated resource. 

In the models.py file, the 'NetcdfMetaData' container class can be defined as shown below:

```
class NetcdfMetaData(CoreMetaData):
    variables = generic.GenericRelation(Variable)
   
    @classmethod
    def get_supported_element_names(cls):
        # get the names of all core metadata elements
        elements = super(NetcdfMetaData, cls).get_supported_element_names()
        # add the name of any additional element to the list
        elements.append('Variable')
        return elements
    
    def get_xml(self):
        from lxml import etree
        # get the xml string representation of the core metadata elements
        xml_string = super(NetcdfMetaData, self).get_xml(pretty_print=False)

        # create an etree xml object
        RDF_ROOT = etree.fromstring(xml_string)

        # get root 'Description' element that contains all other elements
        container = RDF_ROOT.find('rdf:Description', namespaces=self.NAMESPACES)

        # inject netcdf resource specific metadata element 'variable' to container element
        # example xml code for one variable metadata based on the following python code 
        # <hsterms:netcdfVariable>
        # <rdf:Description>
        # <hsterms:name>SWE</hsterms:name>
        # <hsterms:unit>m</hsterms:unit>
        # <hsterms:type>float</hsterms:type>
        # <hsterms:shape>y,x,time</hsterms:shape>
        # <hsterms:descriptiveName>snow water equivalent</hsterms:descriptiveName>
        # <hsterms:method>UEB model simulation for TWDEF site</hsterms:method>
        # <hsterms:missingValue>-9999</hsterms:missingValue>
        # </rdf:Description>
        # </hsterms:netcdfVariable>

        for variable in self.variables.all():
            hsterms_variable = etree.SubElement(container, '{%s}netcdfVariable' % self.NAMESPACES['hsterms'])
            hsterms_variable_rdf_Description = etree.SubElement(hsterms_variable, '{%s}Description' % self.NAMESPACES['rdf'])

            hsterms_name = etree.SubElement(hsterms_variable_rdf_Description, '{%s}name' % self.NAMESPACES['hsterms'])
            hsterms_name.text = variable.name

            hsterms_unit = etree.SubElement(hsterms_variable_rdf_Description, '{%s}unit' % self.NAMESPACES['hsterms'])
            hsterms_unit.text = variable.unit

            hsterms_type = etree.SubElement(hsterms_variable_rdf_Description, '{%s}type' % self.NAMESPACES['hsterms'])
            hsterms_type.text = variable.type

            hsterms_shape = etree.SubElement(hsterms_variable_rdf_Description, '{%s}shape' % self.NAMESPACES['hsterms'])
            hsterms_shape.text = variable.shape

            if variable.descriptive_name:
                hsterms_descriptive_name = etree.SubElement(hsterms_variable_rdf_Description,'{%s}descriptiveName' % self.NAMESPACES['hsterms'])
                hsterms_descriptive_name.text = variable.descriptive_name

            if variable.method:
                hsterms_method = etree.SubElement(hsterms_variable_rdf_Description, '{%s}method' % self.NAMESPACES['hsterms'])
                hsterms_method.text = variable.method

            if variable.missing_value:
                hsterms_missing_value = etree.SubElement(hsterms_variable_rdf_Description, '{%s}missingValue' % self.NAMESPACES['hsterms'])
                hsterms_missing_value.text = variable.missing_value

        return etree.tostring(RDF_ROOT, pretty_print=True)
```

  
**NOTE:** In the above additional example element "Variable", we have assumed that it is a repeatable element. Meaning our "NetcdfResource" can have more than one variable instance. But if want that this resource can have only one variable instance, then it is better to associate the "Variable" element with the container class (NetcdfMetaData) similar to how the "Title" element has been associated with the "CoreMetaData" container class in hs_core (note the additional 'title' property in the container class to access only one element from the collection). Please refer to the implementation of various core metadata element classes and the "CoreMetaData" container class for further help.
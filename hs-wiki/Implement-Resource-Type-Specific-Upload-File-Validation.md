Author: **Pabitra Dash**

Version:**DRAFT**

The following 2 methods in **AbstractResource** class are used for client side validation of uploaded file. These methods are inherited by each resource type class in HydroShare:

    @classmethod
    def get_supported_upload_file_types(cls):
        # NOTES FOR ANY SUBCLASS OF THIS CLASS TO OVERRIDE THIS FUNCTION:
        # to allow only specific file types return a tuple of those file extensions (ex: return (".csv", ".txt",))
        # to not allow any file upload, return a empty tuple ( return ())

        # by default all file types are supported
        return (".*",)


    @classmethod
    def can_have_multiple_files(cls):
        # NOTES FOR ANY SUBCLASS OF THIS CLASS TO OVERRIDE THIS FUNCTION:
        # to allow resource to have only 1 file or no file, return False

        # resource by default can have multiple files
        return True

If your resource type resource can have any file types and more than one file (similar to GenericResource) then you don't need to modify code in your resource type app to implement upload file validation.

However, most likely your resource type only supports specific file types and may have only one file. The comments provided in the above 2 methods should help you to override these 2 methods in your resource class definition to meet the file validation requirements of your resource type.

You can also refer to the **"hs_app_timeseries"** to see how **TimeSeriesResource** class overrides these 2 methods .

There is also provision for server side file type/file content validation.The **pre_create_resource** signal now sends the following additional dictionary which a resource type can listen to and in the case of invalid files need to change the values of this dictionary:

    # receivers need to change the values of this dict if file validation fails
    # e.g., {'are_files_valid': False, 'message': "Invalid files"}
    file_validation_dict = {'are_files_valid': True, 'message': 'Files are valid'}

If any resource type sets the key **'are_file_valid'** of the above dictionary to **False**, then the resource will not be created. You can see an example of server side file validation using the **pre_create_resource** signal handler in **hs_app_timeseries** app in **receivers.py**.

**TODO:**
Document how server side validation will work when uploading a file to an existing resource.
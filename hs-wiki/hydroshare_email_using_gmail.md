# HydroShare Email using Gmail

### Email Configuration

In your **local_settings.py** file, you will find a section like this:

```
# Email configuration
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST_USER = 'hydroshare@hydroshare.org'
EMAIL_HOST_PASSWORD = 'PASSWORD'
EMAIL_HOST = 'EMAIL_HOSTING_SERVICE'
EMAIL_PORT = 'EMAIL_PORT_NUMBER'
EMAIL_USE_TLS= True
DEFAULT_FROM_EMAIL= 'hydroshare@hydroshare.org'
```

### Using Gmail

In order to use Gmail as your email server, there are a few steps that need to occur.

1. Generate an application specific password
    - How to [Generate application specific password](https://support.google.com/mail/answer/1173270?hl=en) 
    - Use this password as the `APP_SPECIFIC_PASSWORD` as shown below
2. Ensure port 587 is open on the VM you are using
3. Update the Email configuration section

    ```
# Email configuration
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST_USER = 'YOUR_GMAIL_ACCOUNT@gmail.com'
EMAIL_HOST_PASSWORD = 'APP_SPECIFIC_PASSWORD'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
```
4. Redeploy your HydroShare application

**NOTE:** Make sure that you do NOT check this file into the Github reposoitory!

### Alternate Email options

Django [documentation](https://docs.djangoproject.com/en/1.7/topics/email/#console-backend)

**Console backend**

Instead of sending out real emails the console backend just writes the emails that would be sent to the standard output. By default, the console backend writes to stdout. You can use a different stream-like object by providing the stream keyword argument when constructing the connection.

To specify this backend, put the following in your settings:

```
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```
This backend is not intended for use in production – it is provided as a convenience that can be used during development.

**File backend**

The file backend writes emails to a file. A new file is created for each new session that is opened on this backend. The directory to which the files are written is either taken from the EMAIL_FILE_PATH setting or from the file_path keyword when creating a connection with get_connection().

To specify this backend, put the following in your settings:

```
EMAIL_BACKEND = 'django.core.mail.backends.filebased.EmailBackend'
EMAIL_FILE_PATH = '/tmp/app-messages' # change this to a proper location
```
This backend is not intended for use in production – it is provided as a convenience that can be used during development.


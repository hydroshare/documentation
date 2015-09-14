# HydroShare REST API

Contents:
- [Design document](#design-document)
- [Implemented REST end points](#implemented-rest-end-points)
- [Python client library](#python-client-library)

## Design document
The original design document for the REST API can be found [here](/hydroshare/hydroshare/blob/master/docs/DesignDocs/API/index.md). Note that the API implementation differs from the specification in several ways.  Firstly, the entire API has not yet been implemented.  Secondly, some minor details of the implementation differ from the specification for practical reasons (e.g. ease implementation given the current state of HydroShare).  Therefore, the [implemented REST end points](#implemented-rest-end-points) should be used as authoritative documentation.

## Implemented REST end points

- getResourceTypes: GET http://www.hydroshare.org/hsapi/resourceTypes/
- getResourceList: GET http://www.hydroshare.org/hsapi/resourceList/
  - Supported filters:
    - creator (HydroShare user ID)
    - owner (HydroShare user ID)
    - user (HydroShare user ID)
    - group (HydroShare group ID)
    - type (HydroShare resource type)
    - from_date (created since date)
    - to_date (created after date)
- getSystemMetadata: GET http://www.hydroshare.org/hsapi/sysmeta/RESOURCE_ID/
  - Return essential system metadata for a resource
- getResource: GET http://www.hydroshare.org/hsapi/resource/RESOURCE_ID/
  - Get zipped BagIt archive representation of a resource
- createResource: POST http://www.hydroshare.org/hsapi/resource/
- deleteResource: DELETE http://www.hydroshare.org/hsapi/resource/RESOURCE_ID/
- setAcccessRules: PUT http://www.hydroshare.org/hsapi/resource/accessRules/RESOURCE_ID/
  - Supported rules:
    - public=true|false
- addResourceFile: POST http://www.hydroshare.org/hsapi/resource/RESOURCE_ID/files/
- getResourceFile: GET http://www.hydroshare.org/hsapi/resource/RESOURCE_ID/files/FILENAME
- deleteResourceFile: DELETE http://www.hydroshare.org/hsapi/resource/RESOURCE_ID/files/FILENAME

## Python client library
Installation and usage instructions for the Python client library can be found [here](https://github.com/hydroshare/hs_restclient).
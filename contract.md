# Mapper Service Contract

## Table of Contents
- [Version](#version)
- [Description](#description)
- [Owners](#owners)
- [Security](#security)
- [Application Programming Interface (API)](#application-programming-interface-api)
  1. [Retrieval of Items](#retrieval-routes)


## Version
Currently, resources are at version 1 (v1). All requests to the resources must be prefixed with v1/

***

## Description


***

## Owners
Mapper Services Team
***

## Security
Authentication - mAuth process to verify that all requests are authentic. 
The Authorization process authorizes all requests using Authorization records using Whitelisting of Apps.

***

## Application Programming Interface (API)
### CatalogItem Resource
There is a single resource available for the Services to generate a mCatalog Item - this uniquely identifies a file/blob inside mStore. We also allow the Services to query the data store to retrieve the metadata for files/blobs stored inside the mStore.

#### Retrievel Route
**POST: `/v1/mapper`**

*Request Body*
```json
{
  "mapper_request_id": "<com:mdsol:mapper_request:uuid>",
  "num_of_suggestions": "10",
  "request_locale": "en_US",
  "coding_requests": [
    {
      "coding_request_id": 1,
      "verbatim_term": "head pain",
      "dictionary": {
        "type": "<MedDRA, WHODrug>",
        "version": "<20>",
        "locale": "<EN,JA,DE>",
        "use_cache": true
      }
    },
    {
      "coding_request_id": 2,
      "verbatim_term": "head wound",
      "dictionary": {
        "type": "<MedDRA, WHODrug>",
        "version": "<20>",
        "locale": "<EN,JA,DE>",
        "use_cache": false
      }
    }
  ]
}
```


#### Body Attributes
The following attributes are used when creating a CatalogItem into the persistent data store

|Attributes                   |Mandatory|Description                                                        |
|:----------------------------|:-------:|:------------------------------------------------------------------|
|mapper_request_id            |   Yes   |Universally unique identifier of the Study the item is part of     |
|num_of_suggestions           |   Yes   |Universally unique identifier of the Client Division               |
|request_locale               |   Yes   |URI for the item on the persisted data store                       |
|coding_request_id            |   Yes   |Name of the item on the persisted data store                       |
|verbatim_term                |   Yes   |UTC Timestamp in ISO 8601 format of the time the item was uploaded |
|dictionary:type              |   Yes   |UTC Timestamp in ISO 8601 format of the time the item was uploaded |
|dictionary:version           |   Yes   |UTC Timestamp in ISO 8601 format of the time the item was uploaded |
|dictionary:locale            |   No    |UTC Timestamp in ISO 8601 format of the time the item was uploaded |
|dictionary:use_cache         |   No    |UTC Timestamp in ISO 8601 format of the time the item was uploaded |


*Response Body*

`201` successful response
```json
{
  "mapper_response_id" : "<com:mdsol:mapper_request:uuid>", 
  "response_status" : "SUCCESS", 
  "coding_suggestions": 
  [
   	{
      "coding_response_id": "1",
      "coding_status" : "pass", 
      "coding_source" : "cache", 
   	  "verbatim_term": "head pain",
     	"suggestions": 
     	[
         {
           	"suggested_term": "Headache",
           	"rank": 100
         },
         {
           	"suggested_term": "Head pain",
           	"rank": 90
         },
         {
           	"suggested_term": "Head throbbing",
           	"rank": 80
         }
   	  ]
   	},
   	{
      "coding_response_id": "2",
      "coding_status" : "pass", 
      "coding_source" : "cache", 
   	  "verbatim_term": "head wound",
     	"suggestions": 
     	[
         {
           	"suggested_term": "head injury",
           	"rank": 100
         },
         {
           	"suggested_term": "head trauma",
           	"rank": 90
         },
         {
           	"suggested_term": "head contusion",
           	"rank": 80
         }
   	  ]
   	}
  ]
}
```

`409` conflict - duplicate item

If request is made to create a new CatalogItem that has been previously created (a combination of study_environment_uuid and file name), then a 409 response is returned
```json
{
  "message": "Item already exists"
}
```

`422` missing parameters response

The mCatalog Service will raise a 422 if any of mandatory attributes are missing or there are invalid attributes -
1. `mandatory_attributes_missing` - a list of mandatory attributes missing in the request
2. `invalid_attributes` - if the values of the attributes are not a valid format then an appropriate message is returned. This is true for optional attributes as well. While some timestamp attributes are optional - if provided, it is recommended that the Service use [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) UTC format.
```json
{
  "mandatory_attributes_missing": [
    "item_name",
    "item_cataloged_at"
  ],
  "invalid_attributes": {
    "item_cataloged_at": "Invalid date format"
  }
}
```

*Response Codes:*
* 200: Successfully retrieved
* 404: Resource not found
* 406: Unsupported content type of request if other than `application/json; charset=utf-8`
* 409: Duplicate item creation attempted
* 422: Missing parameters or invalid values, invalid arn location
* 500: Internal server error, if one of the dependent services returns internal server error.
* 503: Service temporarily unavailable, the client is recommended to wait and try after Retry_After header seconds.

***


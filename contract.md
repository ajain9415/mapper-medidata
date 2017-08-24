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
### Mapper Service


#### Retrievel Route
**POST: `/v1/autocoding_suggestions`**

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
        "locale": "<EN,JA,DE>"
     }
    },
    {
      "coding_request_id": 2,
      "verbatim_term": "head wound",
      "dictionary": {
        "type": "<MedDRA, WHODrug>",
        "version": "<20>",
        "locale": "<EN,JA,DE>"   
      }
    }
  ]
}
```


#### Body Attributes
The following attributes are used when requesting for coded terms for a verbatim 

|Attributes                   |Mandatory|Description                                                            |
|:----------------------------|:-------:|:----------------------------------------------------------------------|
|mapper_request_id            |   Yes   |Unique UUID for each request                                           |
|num_of_suggestions           |   Yes   |Maximum number of suggestions returned for each verbatim term          |
|request_locale               |   Yes   |URI for the item on the persisted data store                           |
|coding_request_id            |   Yes   |Coding Request Id (1,2,3,4 … n)                                        |
|verbatim_term                |   Yes   |Verbatim term value.                                                   |
|dictionary:type              |   Yes   |Dictionary type to use for finding suggestions.  i.e. MedDRA, WHODrug  |
|dictionary:version           |   Yes   |Dictionary version number to use for finding suggestions.              |
|dictionary:locale            |   No    |Dictionary locale to use for finding suggestions.  i.e. EN, JA, DE     |


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

#### Body Attributes
The following attributes are returned in the response JSON 

|Attributes                   |Mandatory|Description                                                            |
|:----------------------------|:-------:|:----------------------------------------------------------------------|
|mapper_response_id           |   Yes   |This uuid is same as corresponding mapper_request_id                   |
|response_status              |   Yes   |Response Status (SUCCESS, FAIL, PARTIAL)                               |
|coding_response_id           |   Yes   |Coding Response Id used to link back to originating coder_request_id   |
|coding_status                |   Yes   |Allowed values (pass,fail)                                             |
|coding_source                |   Yes   |Verbatim term value                                                    |
|verbatim_term                |   Yes   |Verbatim term value                                                    |
|suggested_term               |   Yes   |Suggested term value.                                                  |
|rank                         |   Yes    |Rank of suggested term value.                                          |



*Response Codes:*
* 200: Successfully retrieved
* 404: Resource not found
* 406: Unsupported content type of request if other than `application/json; charset=utf-8`
* 409: Duplicate item creation attempted
* 422: Missing parameters or invalid values, invalid arn location
* 500: Internal server error, if one of the dependent services returns internal server error.
* 503: Service temporarily unavailable, the client is recommended to wait and try after Retry_After header seconds.

***


# Sense API

## Introduction

The Sense API allows third party developers to access the majority of the Sense platform's functionality. It is a RESTful JSON API that accepts HTTP requests.



## Authentication

The API authenticates requests with a user-specific JSON Web Token. Users can generate a JSON Web Token by sending a valid email and password combination to the "login" endpoint. N.B: Users MUST validate their email addresses to create accounts.

> The user logs in with their email and password combination.

**POST /v2/login**
```shell
curl -X POST https://sense-api.sixgill.com/v2/login -H 'content-type: application/json' -d '{
    "email":"1513202525@sixgill.com",
    "password":"password1"
}'
```

> A 200 OK response returns the generated authentication token.

```json
{    "token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoibHNwZWFyc0BzaXhnaWxsLmNvbSIsIm5hbWUiOiJMb2dhbiBTcGVhcnMifSwiZXhwIjoxNTEwMjcwNDM4LCJzdWIiOiIwMUJWUThXQldRMlhONTI4TjZOQzZTRE5SWCIsInRva2VuVHlwZSI6ImFjY2VzcyJ9.5adY3p2pNHXtF4-TKc_wtkJaokl2o9hJVLZvEHJ9klo"
}
```

Once a token has been generated, one can authenticate requests by placing it in the authorization header.

> Example /v2/users/me request with authorization header which will return a 200 OK

**GET /v2/users/me**
```shell
curl "https://sense-api.sixgill.com/v2/users/me" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoibHNwZWFyc0BzaXhnaWxsLmNvbSIsIm5hbWUiOiJMb2dhbiBTcGVhcnMifSwiZXhwIjoxNTEwMjcwNDM4LCJzdWIiOiIwMUJWUThXQldRMlhONTI4TjZOQzZTRE5SWCIsInRva2VuVHlwZSI6ImFjY2VzcyJ9.5adY3p2pNHXtF4-TKc_wtkJaokl2o9hJVLZvEHJ9klo"
```

Response:
```json
{
	"data": {
		"id": "01C6907VRHRHFZCM5C5V2KP27J",
		"email": "1513202525@sixgill.com",
		"name": "Adam Smith",
		"verifiedEmail": true,
		"inviteOutstanding": false,
		"locale": {
			"timezone": "America/Detroit"
		}
	}
}
```

> Example /v2/users/me request without authorization header which will return a 400 Bad Request. Invalid tokens will return 401 Unauthorized

**GET /v2/users/me**
```shell
curl GET https://sense-api.sixgill.com/v2/users/me
```
Note that certain calls require a token that includes the organization ID, such as creating a rule. 

> Get the organization ID:
```shell
curl -X GET http://sense-api-kone.sixgill.run/v2/organizations \
  -H 'authorization: Bearer JSONWEBTOKEN_1' \
  -H 'content-type: application/json'
```  
  
> and make the login request with the organizationId:
```shell
curl -X POST http://sense-api-kone.sixgill.run/v2/login -H 'content-type: application/json' -d '{
    "email": "1513202525@sixgill.com",
    "organizationId": "01CBTBPBV4M6PK2VEGY56N8NN1",
    "password": "password"
}'
```

## Routes TOC
Method | Endpoint | Code | Link
-|-|-|-
POST | /v2/signup | 204 | [link](#post-v2signup)
POST | /v2/verify-email | 200 | [link](#post-v2verify-email)
POST | /v2/login | 200 | [link](#post-v2login)
PUT | /v2/users/me | 200 | [link](#put-v2usersme)
GET | /v2/users/me | 200 | [link](#get-v2usersme)
GET | /v2/organizations | 200 | [link](#get-v2organizations)
GET | /v2/organizations/:organizationID | 200 | [link](#get-v2organizationsorganizationid)
POST | /v2/login | 200 | [link](#post-v2login)
POST | /v2/login | 200 | [link](#post-v2login-with-org)
POST | /v2/setOrganization | 200 | [link](#post-v2setorganization)
POST | /v2/invite | 204 | [link](#post-v2invite)
POST | /v2/accept-invite | 200 | [link](#post-v2accept-invite)
GET | /v2/users | 200 | [link](#get-v2users)
GET | /v2/users/:userID | 200 | [link](#get-v2usersuserid)
POST | /v2/channels | 201 | [link](#post-v2channels)
GET | /v2/channels/:channelID | 200 | [link](#get-v2channelschannelid)
GET | /v2/channels | 200 | [link](#get-v2channels)
POST | /v2/channels/:channelID/apiKeys | 201 | [link](#post-v2channelschannelidapikeys)
PUT | /v2/channels/:channelID/apiKeys/:apiKeyID | 200 | [link](#put-v2channelschannelidapikeysapikeyid)
GET | /v2/channels/:channelID/apiKeys | 200 | [link](#get-v2channelschannelidapikeys)
DELETE | /v2/channels/:channelID/apiKeys/:apiKeyID | 204 | [link](#delete-v2channelschannelidapikeysapikeyid)
POST | /v2/channels/:channelID/devices | 201 | [link](#post-v2channelschanneliddevices)
POST | /v2/channels/:channelID/devices | 201 | [link](#post-v2channelschanneliddevices)
POST | /v2/channels/:channelID/devices | 201 | [link](#post-v2channelschanneliddevices)
GET | /v2/channels/:channelID/devices | 200 | [link](#get-v2channelschanneliddevices)
GET | /v2/devices | 200 | [link](#get-v2devices)
GET | /v2/devices/:deviceID | 200 | [link](#get-v2devicesdeviceid)
POST | /v2/projects | 201 | [link](#post-v2projects)
GET | /v2/projects/:projectID | 200 | [link](#get-v2projectsprojectid)
PUT | /v2/projects/:projectID | 200 | [link](#put-v2projectsprojectid)
GET | /v2/projects | 200 | [link](#get-v2projects)
DELETE | /v2/projects/:projectID/channels/:channelID | 204 | [link](#delete-v2projectsprojectidchannelschannelid)
POST | /v2/projects/:projectID/channels/:channelID | 201 | [link](#post-v2projectsprojectidchannelschannelid)
GET | /v2/projects/:projectID/channels | 200 | [link](#get-v2projectsprojectidchannels)
POST | /v2/projects/:projectID/landmarks | 201 | [link](#post-v2projectsprojectidlandmarks)
POST | /v2/projects/:projectID/landmarks | 201 | [link](#post-v2projectsprojectidlandmarks)
POST | /v2/projects/:projectID/landmarks | 201 | [link](#post-v2projectsprojectidlandmarks)
GET | /v2/projects/:projectID/landmarks/:landmarkID | 200 | [link](#get-v2projectsprojectidlandmarkslandmarkid)
GET | /v2/projects/:projectID/landmarks | 200 | [link](#get-v2projectsprojectidlandmarks)
POST | /v2/landmarks/search | 200 | [link](#post-v2landmarkssearch)
POST | /v2/landmarks/search | 200 | [link](#post-v2landmarkssearch)
DELETE | /v2/projects/:projectID/landmarks/:landmarkID | 204 | [link](#delete-v2projectsprojectidlandmarkslandmarkid)
POST | /v2/projects/:projectID/rules | 201 | [link](#post-v2projectsprojectidrules)
GET | /v2/projects/:projectID/rules | 200 | [link](#get-v2projectsprojectidrules)
GET | /v2/projects/:projectID/rules/:ruleID | 200 | [link](#get-v2projectsprojectidrulesruleid)
GET | /v2/rules/help | 200 | [link](#get-v2ruleshelp)
DELETE | /v2/projects/:projectID/rules/:ruleID | 204 | [link](#delete-v2projectsprojectidrulesruleid)
DELETE | /v2/projects/:projectID/channels/:channelID | 204 | [link](#delete-v2projectsprojectidchannelschannelid)
DELETE | /v2/projects/:projectID | 204 | [link](#delete-v2projectsprojectid)

## Examples
##### POST /v2/signup
```json
{
    "email": "1513202525@sixgill.com",
    "locale": {
        "timezone": "Local"
    },
    "name": "bossman",
    "organizationName": "gillz",
    "password": "password1"
}
```
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/verify-email
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTUxMzIwMjUyNUBzaXhnaWxsLmNvbSIsIm5hbWUiOiJib3NzbWFuIn0sImV4cCI6MTUxMzQ2MTcwMywic3ViIjoiMDFDMThZQktDSktBTVlRNlFRWlNQRjFXR0MiLCJ0b2tlblR5cGUiOiJ2ZXJpZnkifQ.0bUUIyfdpfsVPn-mzkj_4uJ9-hOFdXCm5wDVTJ5ZKxo"
}
```
`Code: 200`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/login
```json
{
    "email": "1513202525@sixgill.com",
    "password": "password1"
}
```
###### POST /v2/login (with organization)
```json
{
    "email": "1513202525@sixgill.com",
    "organizationId": "01C18YBKCHTPM5DSVTEQD5ZEAZ",
    "password": "password1"
}
```
`Code: 200`
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTUxMzIwMjUyNUBzaXhnaWxsLmNvbSIsIm5hbWUiOiJib3NzbWFuIn0sImV4cCI6MTUxNTc5NDUwMywic3ViIjoiMDFDMThZQktDSktBTVlRNlFRWlNQRjFXR0MiLCJ0b2tlblR5cGUiOiJhY2Nlc3MifQ.l-LF543kLWBUdVDNZeh7QNE8Q_9iQnh2Dn89RmS-OpY"
}
```
[:arrow_up:TOC](#routes-toc)
##### PUT /v2/users/me
```json
{
    "locale": {
        "timezone": "America/Los_Angeles"
    },
    "name": "bossman"
}
```
`Code: 200`
```json
{
    "data": {
        "email": "1513202525@sixgill.com",
        "id": "01C18YBKCJKAMYQ6QQZSPF1WGC",
        "inviteOutstanding": false,
        "locale": {
            "timezone": "America/Los_Angeles"
        },
        "name": "bossman",
        "verifiedEmail": true
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/users/me
`Code: 200`
```json
{
	"data": {
		"id": "01CBTBPBV78F5YPVA5JZPWNA52",
		"email": "test@test.com",
		"name": "John",
		"verifiedEmail": true,
		"inviteOutstanding": false,
		"locale": {
			"timezone": "America/Los_Angeles"
		},
		"createdAt": "2018-04-23T22:58:50Z",
		"updatedAt": "2018-04-23T22:59:48Z"
	}
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/organizations
`Code: 200`
```json
{
	"data": [{
		"id": "01CBTBPBV4M6PK2VEGY56N8NN2",
		"name": "My Organization",
		"permissions": {
			"admin": true
		},
		"createdAt": "0001-01-01T00:00:00Z",
		"updatedAt": "2018-04-23T22:58:50Z"
	}],
	"meta": {
		"count": 1,
		"nextIndex": "01CBTBPBV4M6PK2VEGY56N8NN6",
		"nextHRef": "index=01CBTBPBV4M6PK2VEGY56N8NN5\u0026count=50\u0026sortBy=id\u0026sortAsc=false"
	}
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/organizations/:organizationID
`Code: 200`
```json
{
    "data": {
        "id": "01C18YBKCHTPM5DSVTEQD5ZEAZ",
        "name": "My Organization"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/setOrganization
```json
{
    "organizationId": "01C18YBKCHTPM5DSVTEQD5ZEAZ"
}
```
`Code: 200`
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTUxMzIwMjUyNUBzaXhnaWxsLmNvbSIsIm5hbWUiOiJib3NzbWFuIiwicGVybWlzc2lvbnMiOnsiYWRtaW4iOnRydWV9fSwiZXhwIjoxNTE1Nzk0NTA0LCJvcmdJZCI6IjAxQzE4WUJLQ0hUUE01RFNWVEVRRDVaRUFaIiwic3ViIjoiMDFDMThZQktDSktBTVlRNlFRWlNQRjFXR0MiLCJ0b2tlblR5cGUiOiJhY2Nlc3MifQ.V1UfpC7GZldKYzl_bjRDdFqMmvMn_dsfXZukOAZrg3c"
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/invite
```json
{
    "emails": [
        "1513202525Invite@sixgill.com"
    ]
}
```
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/accept-invite
```json
{
    "locale": {
        "timezone": "America/Los_Angeles"
    },
    "name": "Jane Doe",
    "password": "password1",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTUxMzIwMjUyNUludml0ZUBzaXhnaWxsLmNvbSIsIm9yZ2FuaXphdGlvbklkIjoiMDFDMThZQktDSFRQTTVEU1ZURVFENVpFQVoiLCJvcmdhbml6YXRpb25OYW1lIjoiZ2lsbF9nb2QifSwiZXhwIjoxNTEzODA3MzA0LCJzdWIiOiIwMUMxOFlCTUJRQUJDREQ5RUJaRTNDNjZEMyIsInRva2VuVHlwZSI6Imludml0ZSJ9.RAJD5wyAHlnH-xqeZ5raWB_dr2SKVeKqDgLGN1Q09qo"
}
```
`Code: 200`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/users
`Code: 200`
```json
{
    "data": [
        {
            "email": "1513202525@test.com",
            "id": "01C18YBKCJKAMYQ6QQZSPF1WGC",
            "inviteOutstanding": false,
            "locale": {
                "timezone": "America/Los_Angeles"
            },
            "name": "Sam Adams",
            "verifiedEmail": true
        },
        {
            "email": "1513202525Invite@test.com",
            "id": "01C18YBMBQABCDD9EBZE3C66D3",
            "inviteOutstanding": false,
            "locale": {
                "timezone": "America/Los_Angeles"
            },
            "name": "Michael Smith",
            "verifiedEmail": true
        }
    ],
    "meta": {
        "count": 2,
        "nextHRef": "index=01C18YBMBQABCDD9EBZE3C66D3\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBMBQABCDD9EBZE3C66D3"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/users/:userID
`Code: 200`
```json
{
    "data": {
        "email": "1513202525@test.com",
        "id": "01C18YBKCJKAMYQ6QQZSPF1WGC",
        "inviteOutstanding": false,
        "locale": {
            "timezone": "America/Los_Angeles"
        },
        "name": "Sam Adams",
        "verifiedEmail": true
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/channels
```json
{
    "name": "android channel",
    "type": "Android"
}
```
`Code: 201`
```json
{
    "data": {
        "apiKeys": [
            {
                "apiKey": "rCy9ftLB41/6ZBuP3W9zj9uv",
                "createdAt": "0001-01-01T00:00:00Z",
                "enabled": true,
                "id": "01C18YBMV8DJ6ZJKB6RP1KCZD1",
                "qrCode": ""
            }
        ],
        "enabled": false,
        "id": "01C18YBMV80QPD89HR0BFY0T0A",
        "name": "android channel",
        "type": "Android"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/channels/:channelID
`Code: 200`
```json
{
    "data": {
        "enabled": false,
        "id": "01C18YBMV80QPD89HR0BFY0T0A",
        "name": "rick",
        "type": "Android"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/channels
`Code: 200`
```json
{
    "data": [
        {
            "enabled": false,
            "id": "01C18YBMV80QPD89HR0BFY0T0A",
            "name": "rick",
            "type": "Android"
        }
    ],
    "meta": {
        "count": 1,
        "nextHRef": "index=01C18YBMV80QPD89HR0BFY0T0A\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBMV80QPD89HR0BFY0T0A"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/channels/:channelID/apiKeys
`Code: 201`
```json
{
    "data": {
        "apiKey": "n++6wKD0yl0G5WMWzqFLdRHi",
        "createdAt": "2017-12-13T22:01:44.666003468Z",
        "enabled": true,
        "id": "01C18YBMYS49WM6VJWKBZ3TKKJ",
        "qrCode": "iVBORw0KGgoAAAANSUhEUgAAAQAAAAEACAMAAABrrFhUAAAABlBMVEX///8AAABVwtN+AAAB8UlEQVR42uzcMXqrMBCF0WT/m06dhu+OhNCAzt+98GLjkwJGlv0jSZIkSZIkSZIkSZIkSZL0Wy14sPzYrc8OAAAAAAAADAKM/s//PwwA8l8YPU8AAAAAAABgEODq2hsATD5m+dkBAAAAAACArQD5dBvcOAAAAAAAAABvBsjXegEAAAAAAIB+AGWqqyXfby+LAwAAAACADwGUd+nc+q83bpICAAAAAABfAFhxG9H1ZAEAAAAAAID6Kyiv544+AwAAAAAAANDhorjiHcx8yN05DgMAAAAAgLMAyudVXs8NiCdtAQAAAAAAgHVT8Yods/lw/NCNEAAAAAAAOBIgHzrLx/K15Z1rxAAAAAAA4GSAfFjN5+DJO43ynwYAAAAAAAC4cw4ePYXyHqH8MQEAAAAAAICnVoVHd74GY+3k3iIAAAAAAADgqVXh/NjkkLvziwMAAAAAAMDJAOvhyuN3+RgAAAAAAABw5wtZ8TGSq18IRmUAAAAAAACg7XeLr7jW7/ysDAAAAAAAOBIgv4LnOPmtAgAAAAAAAPASgPKS7+RKMwAAAAAAANDvPmDyk4/luwIAAAAAAACgw6pwPiM3eAMUAAAAAAAAKIygo3t9ylf+/IftNkkBAAAAAIAvAEiSJEmSJEmSJEmSJEmSJL2tvwAAAP//ph89QWgOuOgAAAAASUVORK5CYII="
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### PUT /v2/channels/:channelID/apiKeys/:apiKeyID
```json
{
    "enabled": false
}
```
`Code: 200`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/channels/:channelID/apiKeys
`Code: 200`
```json
{
    "data": [
        {
            "apiKey": "n++6wKD0yl0G5WMWzqFLdRHi",
            "createdAt": "2017-12-13T22:01:45Z",
            "enabled": false,
            "id": "01C18YBMYS49WM6VJWKBZ3TKKJ",
            "qrCode": ""
        }
    ]
}
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/channels/:channelID/apiKeys/:apiKeyID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/channels/:channelID/devices
```json
{
    "properties": {
        "manufacturer": "sixgill",
        "model": "1.4",
        "os": "Android",
        "osVersion": "1.0",
        "softwareVersion": "1.1"
    },
    "sensors": [
        "bluetooth",
        "gps"
    ]
}
```
`Code: 201`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/channels/:channelID/devices
```json
{
    "properties": {
        "manufacturer": "sixgill",
        "model": "1.5",
        "os": "iOS",
        "osVersion": "1.3",
        "softwareVersion": "1.4"
    },
    "sensors": [
        "gps"
    ]
}
```
`Code: 201`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/channels/:channelID/devices
```json
{
    "properties": {
        "manufacturer": "sixgill",
        "model": "1.0",
        "os": "gillOS",
        "osVersion": "1.1",
        "softwareVersion": "1.2"
    },
    "sensors": [
        "bluetooth",
        "wifi",
        "gps"
    ]
}
```
`Code: 201`
```json
{
    "data": {
        "attributes": null,
        "deviceState": null,
        "id": "01C18YBNA6816JTT2A3EVK7MEA",
        "properties": {
            "manufacturer": "sixgill",
            "model": "1.0",
            "os": "gillOS",
            "osVersion": "1.1",
            "softwareVersion": "1.2"
        },
        "sensors": [
            "bluetooth",
            "wifi",
            "gps"
        ],
        "tags": null,
        "type": ""
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/channels/:channelID/devices
`Code: 200`
```json
{
    "data": [
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBN5QABY8W701HETN9WQB",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.4",
                "os": "Android",
                "osVersion": "1.0",
                "softwareVersion": "1.1"
            },
            "sensors": [
                "bluetooth",
                "gps"
            ],
            "tags": null,
            "type": ""
        },
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBN7WE96734M2C41J0FM3",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.5",
                "os": "iOS",
                "osVersion": "1.3",
                "softwareVersion": "1.4"
            },
            "sensors": [
                "gps"
            ],
            "tags": null,
            "type": ""
        },
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBNA6816JTT2A3EVK7MEA",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.0",
                "os": "gillOS",
                "osVersion": "1.1",
                "softwareVersion": "1.2"
            },
            "sensors": [
                "bluetooth",
                "wifi",
                "gps"
            ],
            "tags": null,
            "type": ""
        }
    ],
    "meta": {
        "count": 3,
        "nextHRef": "index=01C18YBNA6816JTT2A3EVK7MEA\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBNA6816JTT2A3EVK7MEA"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/devices
`Code: 200`
```json
{
    "data": [
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBN5QABY8W701HETN9WQB",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.4",
                "os": "Android",
                "osVersion": "1.0",
                "softwareVersion": "1.1"
            },
            "sensors": [
                "bluetooth",
                "gps"
            ],
            "tags": null,
            "type": ""
        },
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBN7WE96734M2C41J0FM3",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.5",
                "os": "iOS",
                "osVersion": "1.3",
                "softwareVersion": "1.4"
            },
            "sensors": [
                "gps"
            ],
            "tags": null,
            "type": ""
        },
        {
            "attributes": null,
            "deviceState": null,
            "id": "01C18YBNA6816JTT2A3EVK7MEA",
            "properties": {
                "manufacturer": "sixgill",
                "model": "1.0",
                "os": "gillOS",
                "osVersion": "1.1",
                "softwareVersion": "1.2"
            },
            "sensors": [
                "bluetooth",
                "wifi",
                "gps"
            ],
            "tags": null,
            "type": ""
        }
    ],
    "meta": {
        "count": 3,
        "nextHRef": "index=01C18YBNA6816JTT2A3EVK7MEA\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBNA6816JTT2A3EVK7MEA"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/devices/:deviceID
`Code: 200`
```json
{
    "data": {
        "attributes": null,
        "deviceState": null,
        "id": "01C18YBNA6816JTT2A3EVK7MEA",
        "properties": {
            "manufacturer": "sixgill",
            "model": "1.0",
            "os": "gillOS",
            "osVersion": "1.1",
            "softwareVersion": "1.2"
        },
        "sensors": [
            "bluetooth",
            "wifi",
            "gps"
        ],
        "tags": null,
        "type": ""
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects
```json
{
    "channelIDs": [
        "01C18YBMV80QPD89HR0BFY0T0A"
    ],
    "name": "ricky"
}
```
`Code: 201`
```json
{
    "data": {
        "channels": [
            {
                "enabled": false,
                "id": "01C18YBMV80QPD89HR0BFY0T0A",
                "name": "rick",
                "type": "Android"
            }
        ],
        "createdAt": "2017-12-13T22:01:45Z",
        "enabled": false,
        "id": "01C18YBNJCE3KBCNZJHKV5R55Y",
        "name": "ricky"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects
`Code: 200`
```json
{
	"data": [{
		"id": "01CBTBSQRNBBDYGREK305FQK6S",
		"name": "Project 1",
		"enabled": false,
		"channels": null,
		"createdAt": "2018-04-23T23:00:41Z"
	}, {
		"id": "01CCM17R0EVDETDX7KV788305D",
		"name": "Project 2",
		"enabled": false,
		"channels": null,
		"createdAt": "2018-05-03T22:16:21Z"
	}, {
		"id": "01CCXYZ646P52K5W95MYPX29MR",
		"name": "Project 3",
		"enabled": false,
		"channels": null,
		"createdAt": "2018-05-07T18:49:07Z"
	}],
	"meta": {
		"count": 3,
		"nextIndex": "01CCXYZ646P52K5W95MYPX29MR",
		"nextHRef": "index=01CCXYZ646P52K5W95MYPX29MR\u0026count=50\u0026sortBy=id\u0026sortAsc=false"
	}
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects/:projectID
`Code: 200`
```json
{
    "data": {
        "channels": [
            {
                "enabled": false,
                "id": "01C18YBMV80QPD89HR0BFY0T0A",
                "name": "rick",
                "type": "Android"
            }
        ],
        "createdAt": "2017-12-13T22:01:45Z",
        "enabled": false,
        "id": "01C18YBNJCE3KBCNZJHKV5R55Y",
        "name": "ricky"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### PUT /v2/projects/:projectID
```json
{
    "name": "ricky"
}
```
`Code: 200`
```json
{
    "data": {
        "channels": null,
        "createdAt": "2017-12-13T22:01:45Z",
        "enabled": false,
        "id": "01C18YBNJCE3KBCNZJHKV5R55Y",
        "name": "ricky"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects
`Code: 200`
```json
{
    "data": [
        {
            "channels": null,
            "createdAt": "2017-12-13T22:01:45Z",
            "enabled": false,
            "id": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "name": "ricky"
        }
    ],
    "meta": {
        "count": 1,
        "nextHRef": "index=01C18YBNJCE3KBCNZJHKV5R55Y\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBNJCE3KBCNZJHKV5R55Y"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/projects/:projectID/channels/:channelID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects/:projectID/channels/:channelID
`Code: 201`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects/:projectID/channels
`Code: 200`
```json
{
    "data": [
        {
            "enabled": false,
            "id": "01C18YBMV80QPD89HR0BFY0T0A",
            "name": "android channel",
            "type": "Android"
        }
    ]
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects/:projectID/landmarks
```json
{
    "address": "test",
    "model": {
        "geometry": {
            "center": {
                "lat": -44,
                "lon": 77
            },
            "radius": 100
        },
        "type": "circle"
    },
    "name": "school",
    "type": "geometry"
}
```
`Code: 201`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects/:projectID/landmarks
```json
{
    "address": "test",
    "model": {
        "geometry": {
            "nw": {
                "lat": 44,
                "lon": -77
            },
            "se": {
                "lat": 0,
                "lon": 0
            }
        },
        "type": "rectangle"
    },
    "name": "rick",
    "type": "geometry"
}
```
`Code: 201`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects/:projectID/landmarks
```json
{
    "address": "test",
    "model": {
        "geometry": {
            "points": [
                {
                    "lat": 50,
                    "lon": 0
                },
                {
                    "lat": 50,
                    "lon": 50
                },
                {
                    "lat": 0,
                    "lon": 50
                },
                {
                    "lat": 0,
                    "lon": 0
                }
            ]
        },
        "type": "polygon"
    },
    "name": "concert hall",
    "type": "geometry"
}
```
`Code: 201`
```json
{
    "data": {
        "address": "test",
        "attributes": null,
        "id": "01C18YBP3KB135C7YM38QZD0TP",
        "model": {
            "geometry": {
                "points": [
                    {
                        "lat": 50,
                        "lon": 0
                    },
                    {
                        "lat": 50,
                        "lon": 50
                    },
                    {
                        "lat": 0,
                        "lon": 50
                    },
                    {
                        "lat": 0,
                        "lon": 0
                    }
                ]
            },
            "type": "polygon"
        },
        "name": "concert hall",
        "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
        "tags": null,
        "type": "geometry"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects/:projectID/landmarks/:landmarkID
`Code: 200`
```json
{
    "data": {
        "address": "test",
        "attributes": null,
        "id": "01C18YBP3KB135C7YM38QZD0TP",
        "model": {
            "geometry": {
                "points": [
                    {
                        "lat": 50,
                        "lon": 0
                    },
                    {
                        "lat": 50,
                        "lon": 50
                    },
                    {
                        "lat": 0,
                        "lon": 50
                    },
                    {
                        "lat": 0,
                        "lon": 0
                    }
                ]
            },
            "type": "polygon"
        },
        "name": "concert hall",
        "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
        "tags": null,
        "type": "geometry"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects/:projectID/landmarks
`Code: 200`
```json
{
    "data": [
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBNYWFZV5XJPHP4M4VSXA",
            "model": {
                "geometry": {
                    "center": {
                        "lat": -44,
                        "lon": 77
                    },
                    "radius": 100
                },
                "type": "circle"
            },
            "name": "gas station",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP175AVMRZG7A5SBKAZG",
            "model": {
                "geometry": {
                    "nw": {
                        "lat": 44,
                        "lon": -77
                    },
                    "se": {
                        "lat": 0,
                        "lon": 0
                    }
                },
                "type": "rectangle"
            },
            "name": "school",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP3KB135C7YM38QZD0TP",
            "model": {
                "geometry": {
                    "points": [
                        {
                            "lat": 50,
                            "lon": 0
                        },
                        {
                            "lat": 50,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 0
                        }
                    ]
                },
                "type": "polygon"
            },
            "name": "concert hall",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        }
    ],
    "meta": {
        "count": 3,
        "nextHRef": "index=01C18YBP3KB135C7YM38QZD0TP\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBP3KB135C7YM38QZD0TP"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/landmarks/search
```json
{
    "name": "rick"
}
```
`Code: 200`
```json
{
    "data": [
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBNYWFZV5XJPHP4M4VSXA",
            "model": {
                "geometry": {
                    "center": {
                        "lat": -44,
                        "lon": 77
                    },
                    "radius": 100
                },
                "type": "circle"
            },
            "name": "school",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP175AVMRZG7A5SBKAZG",
            "model": {
                "geometry": {
                    "nw": {
                        "lat": 44,
                        "lon": -77
                    },
                    "se": {
                        "lat": 0,
                        "lon": 0
                    }
                },
                "type": "rectangle"
            },
            "name": "gas station",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP3KB135C7YM38QZD0TP",
            "model": {
                "geometry": {
                    "points": [
                        {
                            "lat": 50,
                            "lon": 0
                        },
                        {
                            "lat": 50,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 0
                        }
                    ]
                },
                "type": "polygon"
            },
            "name": "concert hall",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        }
    ],
    "meta": {
        "count": 3,
        "nextHRef": "index=01C18YBP3KB135C7YM38QZD0TP\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBP3KB135C7YM38QZD0TP"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/landmarks/search
```json
{
    "name": "rick"
}
```
`Code: 200`
```json
{
    "data": [
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBNYWFZV5XJPHP4M4VSXA",
            "model": {
                "geometry": {
                    "center": {
                        "lat": -44,
                        "lon": 77
                    },
                    "radius": 100
                },
                "type": "circle"
            },
            "name": "gas station",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP175AVMRZG7A5SBKAZG",
            "model": {
                "geometry": {
                    "nw": {
                        "lat": 44,
                        "lon": -77
                    },
                    "se": {
                        "lat": 0,
                        "lon": 0
                    }
                },
                "type": "rectangle"
            },
            "name": "concert hall",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        },
        {
            "address": "test",
            "attributes": null,
            "id": "01C18YBP3KB135C7YM38QZD0TP",
            "model": {
                "geometry": {
                    "points": [
                        {
                            "lat": 50,
                            "lon": 0
                        },
                        {
                            "lat": 50,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 50
                        },
                        {
                            "lat": 0,
                            "lon": 0
                        }
                    ]
                },
                "type": "polygon"
            },
            "name": "school",
            "projectId": "01C18YBNJCE3KBCNZJHKV5R55Y",
            "tags": null,
            "type": "geometry"
        }
    ],
    "meta": {
        "count": 3,
        "nextHRef": "index=01C18YBP3KB135C7YM38QZD0TP\u0026count=50\u0026sortBy=id\u0026sortAsc=false",
        "nextIndex": "01C18YBP3KB135C7YM38QZD0TP"
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/projects/:projectID/landmarks/:landmarkID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### POST /v2/projects/:projectID/rules

See [Rules](http://docs.sixgill.com/rules.html) docs for more information.

```json
{
	"name": "enter gym - push",
	"description": "",
	"throttleInSeconds": 0,
	"actions": [{
		"type": "push",
		"subject": "Welcome to the gym!",
		"message": "Have a great workout"
	}],
	"logicalCondition": {
		"and": [{
			"type": "enter landmark",
			"landmarkId": "01C85HTS8AG7WAYKW24WP74ZY7"
		}]
	},
	"enabled": true
}
```
Response:
`Code: 201`
```json
{
	"data": {
		"id": "01CHR0HHC23F5WYJYHB4JXS8P9",
		"name": "enter gym - push",
		"description": "",
		"enabled": true,
		"throttleInSeconds": 0,
		"logicalCondition": {
			"and": [{
				"type": "enter landmark",
				"landmarkId": "01C85HTS8AG7WAYKW24WP74ZY7"
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "push",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "Welcome to the gym!",
			"message": "Have a great workout"
		}],
		"createdAt": "0001-01-01T00:00:00Z",
		"updatedAt": "0001-01-01T00:00:00Z"
	}
}
```

[:arrow_up:TOC](#routes-toc)

##### GET /v2/projects/:projectID/rules
`Code: 200`
```json
{
	"data": [{
		"id": "01C9M0MWKQ21RADYJYHGW6A741",
		"name": "testing email and push",
		"description": "",
		"enabled": false,
		"logicalCondition": {
			"and": [{
				"type": "inside landmark",
				"landmarkId": "01C8GGCMD2MAZA5B5YGG8J39CS"
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "email",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "inside!",
			"message": "WEEE",
			"recipients": {
				"emails": ["test@test.com"]
			}
		}, {
			"id": "00000000000000000000000000",
			"type": "push",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "inside!",
			"message": ":)"
		}],
		"createdAt": "2018-03-27T15:18:58Z"
	}, {
		"id": "01CA3FSD67SYMDN1A5SE2MS4RS",
		"name": "inside area push",
		"description": "",
		"enabled": true,
		"logicalCondition": {
			"and": [{
				"type": "inside landmark",
				"landmarkId": "01CA3ET7WF78RRRZCSFBATEY7E"
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "push",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "inside area",
			"message": "hello"
		}],
		"createdAt": "2018-04-02T15:32:11Z"
	}, {
		"id": "01CA8AN5Q5G6QKYYWB0FY7P4A5",
		"name": "OR condition",
		"description": "",
		"enabled": true,
		"logicalCondition": {
			"and": [{
				"type": "event occurred between times of day",
				"startInSecondsSinceMidnight": 32400,
				"endInSecondsSinceMidnight": 37800,
				"timeZone": "America/Detroit"
			}, {
				"and": [{
					"type": "inside landmark",
					"landmarkId": "01C83KM04C9MYYYV4AVJNJQZ9G"
				}, {
					"type": "event free form",
					"predicate": "properties.type == 'ios'"
				}]
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "email",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "one of the conditions was met",
			"message": "weee",
			"recipients": {
				"emails": ["test@test.com"]
			}
		}],
		"createdAt": "2018-04-04T12:38:41Z"
	}, {
		"id": "01CATWVEWG0XN2984GPW29WVT9",
		"name": "test webhook 2 actions",
		"description": "",
		"enabled": false,
		"logicalCondition": {
			"and": [{
				"type": "inside landmark",
				"landmarkId": "01C8GGCMD2MAZA5B5YGG8J39CS"
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "webhook",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"url": "http://www.google.com",
			"method": "POST"
		}, {
			"id": "00000000000000000000000000",
			"type": "webhook",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"url": "http://www.google.com",
			"method": "POST"
		}],
		"createdAt": "2018-04-11T17:43:01Z"
	}],
	"meta": {
		"count": 4,
		"nextIndex": "01CATWVEWG0XN2984GPW29WVT9",
		"nextHRef": "index=01CATWVEWG0XN2984GPW29WVT9\u0026count=50\u0026sortBy=id\u0026sortAsc=false"
	}
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/projects/:projectID/rules/:ruleID
`Code: 200`
```json
{
	"data": {
		"id": "01CA3FSD67SYMDN1A5SE2MS4RS",
		"name": "inside area push",
		"description": "",
		"enabled": true,
		"logicalCondition": {
			"and": [{
				"type": "inside landmark",
				"landmarkId": "01CA3ET7WF78RRRZCSFBATEY7E"
			}]
		},
		"actions": [{
			"id": "00000000000000000000000000",
			"type": "push",
			"successfullyExecutedAt": "0001-01-01T00:00:00Z",
			"deviceId": "00000000000000000000000000",
			"subject": "inside area",
			"message": "hello"
		}],
		"createdAt": "2018-04-02T15:32:11Z"
	}
}
```
[:arrow_up:TOC](#routes-toc)
##### GET /v2/rules/help
`Code: 200`
```json
{
    "data": {
        "actions": [
            "no-op",
            "send notification",
            "email",
            "change config"
        ],
        "conditions": [
            "enter area",
            "exit area",
            "always true",
            "always false"
        ]
    }
}
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/projects/:projectID/rules/:ruleID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/projects/:projectID/channels/:channelID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)
##### DELETE /v2/projects/:projectID
`Code: 204`
```json
no content
```
[:arrow_up:TOC](#routes-toc)

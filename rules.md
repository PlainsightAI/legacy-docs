# Rules

```json
{
	"name": "",
	"logicalCondition": {},
	"actions": []
}
```


## Logical Conditions


```json
{
	"and": []
}
```


```json
{
	"or": []
}
```

```json
{
	"not": {}
}
```

###### enter landmark
```json
{
	"type": "enter landmark",
	"landmarkIds": []
}
```

###### exit landmark
```json
{
	"type": "exit landmark",
	"landmarkIds": []
}
```

###### landmark has all tags
```json
{
	"type": "landmark has all tags",
	"channelId": "", // will use this to get an array of landmarks that belong to this channel
	"tags": []
}
```

###### landmark has any tags
```json
{
	"type": "landmark has any tags",
	"channelId": "", // will use this to get an array of landmarks that belong to this channel
	"tags": []
}
```

###### inside landmark
```json
{
	"type": "inside landmark",
	"landmarkIds": []
}
```

###### outside landmark
```json
{
	"type": "outside landmark",
	"landmarkIds": []
}
```

###### event free form
```json
{
	"type": "event free form",
	"predicate": ""
}
```

###### event occurred after
```json
{
	"type": "event occurred after",
	"moment": "2017-01-01T00:00:00Z",
	"timeZone": ""
}
```

###### event occurred before
```json
{
	"type": "event occurred before",
	"moment": "2017-01-01T00:00:00Z",
	"timeZone": ""
}
```

###### event occurred between times of day
```json
{
	"type": "event occurred between times of day",
	"startInSecondsSinceMidnight": 0,
	"endInSecondsSinceMidnight": 0,
	"timeZone": ""
}
```

###### event occurred on day of week
```json
{
	"type": "event occurred on day of week",
	"dayOfWeek": 0,
	"timeZone": ""
}
```

## Actions

###### email
```json
{
	"type": "email",
	"recipients": {}, // recipients object
	"subject": "",
	"body": ""
}
```

###### push
```json
{
	"type": "push",
	"recipients": {}, // recipients object
	"message": ""
}
```

###### SMS
```json
{
	"type": "sms",
	"recipients": {}, // recipients object
	"message": ""
}
```

###### update device state add tags
```json
{
	"type": "update device state add tags",
	"deviceIds": [], // "triggering device"
	"tags": []
}
```

###### update device state remove tags
```json
{
	"type": "update device state remove tags",
	"deviceIds": [], // "triggering device"
	"tags": []
}
```

###### update landmark state add tags
```json
{
	"type": "update landmark state add tags",
	"landmarkIds": [], // "triggering landamrk"
	"tags": []
}
```

###### update landmark state remove tags
```json
{
	"type": "update landmark state remove tags",
	"landmarkIds": [], // "triggering landamrk"
	"tags": []
}
```

### recipients
The recipient object is a map of recipient types, where each key corresponds to an array value of idenitfying recipient information.
```json
{
	"emails": ["sample@sixgill.com", "joe@walsh.com"],
	"telephoneNumbers": ["1234567890"],
	"deviceIds": ["017a9b8c7t0f8dk48dj3lf"],
	"reflect": ["triggering device"]
}
```

## Message Variables
Messages can substitute actual data in place of placeholders by specifying the placeholder (variable) name inside of `${ replace me }`.

##### global variables
Variable Name | Description
-|-
eventTime | time of event
triggeringDevice | access to info about the triggering device
triggeringLandmark | access to info about the triggering landmark


##### triggering device

Example: `${triggeringDevice.lat}`

Variable Name | Description
-|-
id | device id
lat | latitude at time of event
lon | longitude at time of event

##### triggering landmark

Example: `${triggeringLandmark.name}`

Variable Name | Description
-|-
id | landmark id
name | landmark name
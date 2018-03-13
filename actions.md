## Actions

#### email
```json
{
	"type": "email",
	"recipients": {}, // recipients object
	"subject": "",
	"body": ""
}
```

#### push
```json
{
	"type": "push",
	"recipients": {}, // recipients object
	"message": ""
}
```

#### SMS
```json
{
	"type": "sms",
	"recipients": {}, // recipients object
	"message": ""
}
```

#### update device state add tags
```json
{
	"type": "update device state add tags",
	"deviceIds": [], // "triggering device"
	"tags": []
}
```

#### update device state remove tags
```json
{
	"type": "update device state remove tags",
	"deviceIds": [], // "triggering device"
	"tags": []
}
```

#### update landmark state add tags
```json
{
	"type": "update landmark state add tags",
	"landmarkIds": [], // "triggering landamrk"
	"tags": []
}
```

#### update landmark state remove tags
```json
{
	"type": "update landmark state remove tags",
	"landmarkIds": [], // "triggering landamrk"
	"tags": []
}
```

### #recipients
The recipient object is a hash of recipient types whose keys map to arrays of information on the recipients.
```json
{
	"emails": ["sample@sixgill.com", "joe@walsh.com"],
	"telephoneNumbers": ["1234567890"],
	"deviceIds": ["017a9b8c7t0f8dk48dj3lf"],
	"reflect": ["triggering device"]
}
```
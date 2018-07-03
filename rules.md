# Rules

A rule defines the set of automated actions that are performed when one or more conditions for sensor data are met. Sensor data can come from mobile or IoT devices. Rules must be created within a project that includes the desired data channels to be processed. Rules can be used to send an SMS, push, email, or webhook when an event occurs and can be run at certain times of the day.


Example:
```json
{
	"name": "enter gym - push",
	"description": "",
	"throttleInSeconds": 0,
	"actions": [{
		"type": "push",
		"subject": "welcome to the gym!",
		"message": "have a great workout"
	}],
	"logicalCondition": {
		"and": [{
			"type": "enter landmark",
			"landmarkId": "01C85HTS8AG7WAYKW24WP74ZYX"
		}]
	},
	"id": "01C94FMW65G81W0GNSPQ41XGGZ",
	"enabled": true
}
```
Fields:
* name - (required) Name of the rule
* description - (optional) Description of rule
* throttleInSeconds - Specifies the minimum amount of time between executing the action for this rule (in seconds). For example, if set to 300 the action will be executed no more than once every 5 minutes.
* actions - Describes what will occur when the condition is satisfied. See Actions for more details.
* logicalCondition - Describes the conditions that must be met in order to execute the action(s). A condition can be composed of one or more predicates joined with boolean operators. See Conditions for more details.

- 

## Conditions
Conditions are composed of one or more predicates. Predicates can be joined with boolean operators to form a logical expression that can be evaluated.

### Operators
#### "and"

	This is a boolean operator that allows you to combine other predicates with "AND" semantics. In this example, a rule is checking for the specific device to be inside a landmark.

Example:
```json
{
	"name": "inside area test",
	"description": "",
	"throttleInSeconds": 0,
	"actions": [{
		"type": "email",
		"subject": "inside area!",
		"message": "test: {{.Device.ID.String}}",
		"recipients": {
			"emails": ["test@email.com"]
		}
	}],
	"logicalCondition": {
		"and": [{
			"type": "inside landmark",
			"landmarkId": "01C92NZT6WPQ0J87YVQDE72GGS"
		}, {
			"type": "event free form",
			"predicate": "deviceId == '01CGH777EWVP0E6EZZYH7P2JK0'"
		}]
	},
	"enabled": true
}
```

#### "not"

Description:
	This is a boolean operator that allows you to negate a predicate with "NOT" semantics. The following example uses "NOT" to include a schedule when the rule should not be triggered.

Example:
```json
{
    "name": "landmark with excluded schedule",
    "description": "",
    "throttleInSeconds": 0,
    "actions": [{
      "type": "email",
      "subject": "this is NOT always false",
      "message": "test: {{.Device.ID.String}}",
      "recipients": {
        "emails": ["test@email.com"]
      }
    }],
    "logicalCondition": {
    "not": [{
      "type": "event occurred between times of day",
      "timeZone": "America/Detroit",
      "startInSecondsSinceMidnight": 57600,
      "endInSecondsSinceMidnight": 63000
    }, {
      "and": [{
        "type": "inside landmark",
        "landmarkId": "01C92NZT6WPQ0J87YVQDE72GGX"
      }]
    }]
  },
    "enabled":True
    }
```

#### "or"

Description:
	This is a boolean operator that allows you to combine other predicates with "OR" semantics, e.g.:

Fields:
```json
{
	"name": "inside this area or that area",
	"description": "",
	"throttleInSeconds": 0,
	"actions": [{
		"type": "email",
		"subject": "inside one area or the other",
		"message": "test: {{.Device.ID.String}}",
		"recipients": {
			"emails": ["test@email.com"]
		}
	}],
	"logicalCondition": {
		"or": [{
			"type": "inside landmark",
			"landmarkId": "01C85HTS8AG7WAYKW24WP74ZYA"
		}, {
			"type": "inside landmark",
			"landmarkId": "01C92NZT6WPQ0J87YVQDE72GGF"
		}]
	},
	"enabled": true
}
```
### Predicates
#### always_false

Description:
	This is a predicate that is always false.  It is mostly used for testing, e.g.:

Fields:
```json
{
	"type": "always false"
}
```

Usage:
```go
	p && AlwaysFalse.Evaluate() == false, regardless of p's truth value
```

#### always_true

Description:
	This is a predicate that is always true.  It is mostly used for testing, e.g.:


Fields:
```json
{
	"type": "always true"
}
```

Usage:
```go
	p || AlwaysTrue.Evaluate() == true, regardless of p's truth value
```
     
#### event_free_form

Description:
	This is a predicate that allows you to evaluate a specific event field (usually an IoT event), e.g.:

Fields:
```json
{
	"type": "event free form",
	"predicate": ""
}
```

Usage:
```go
	payloadAsString := `{"foo": 1}`
	e := event.NewIotEvent(*ulid.New(), *ulid.New(), eventLocations, clientSentAt, serverReceivedAt, []byte(payloadAsString))
	p, err := EventFreeForm("foo == 1")
	p.Evaluate() == true, if "foo == 1"
	p.Evaluate() == false, if "foo != 1"
```
   
#### enter_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device event entered a landmark that it was not already inside of, e.g.:

Fields:
```json
{
	"type": "enter landmark",
	"landmarkId": ""
}
```

Usage:
```go
	//create landmark
	tl, _ := geo.NewLongLat(-1, 1)
	tr, _ := geo.NewLongLat(1, 1)
	br, _ := geo.NewLongLat(1, -1)
	bl, _ := geo.NewLongLat(-1, -1)
	l = landmark.NewLandmark(*projID, "test", []*geo.Polygon{geo.NewPolygon([]*geo.LongLat{tl, tr, br, bl})})
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create the predicate
	p := EnterLandmark(l.ID.String())
	
	//evaluate the predicate under different scenarios
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowInsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> true
	p.Evaluate(eventIsNowInsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> false
```

#### exit_landmark
   
Description:
	This is a predicate that allows you to evaluate whether or not a device event exited a landmark that it was not already outside of, e.g.:

Fields:
```json
{
	"type": "exit landmark",
	"landmarkId": ""
}
```

Usage:
```go
	//create landmark
	tl, _ := geo.NewLongLat(-1, 1)
	tr, _ := geo.NewLongLat(1, 1)
	br, _ := geo.NewLongLat(1, -1)
	bl, _ := geo.NewLongLat(-1, -1)
	l = landmark.NewLandmark(*projID, "test", []*geo.Polygon{geo.NewPolygon([]*geo.LongLat{tl, tr, br, bl})})
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create the predicate
	p := ExitLandmark(l.ID.String())
	
	//evaluate the predicate under different scenarios
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> true
	p.Evaluate(eventIsNowInsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowInsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> false
```
	
#### inside_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device event is inside of a given landmark, e.g.:

Fields:
```json
{
	"type": "inside landmark",
	"landmarkId": ""
}
```

Usage:
```go
	//create landmark
	tl, _ := geo.NewLongLat(-1, 1)
	tr, _ := geo.NewLongLat(1, 1)
	br, _ := geo.NewLongLat(1, -1)
	bl, _ := geo.NewLongLat(-1, -1)
	l = landmark.NewLandmark(*projID, "test", []*geo.Polygon{geo.NewPolygon([]*geo.LongLat{tl, tr, br, bl})})
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create the predicate
	p := InsideLandmark(l.ID.String())
	
	//evaluate the predicate under different scenarios
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowInsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> true
	p.Evaluate(eventIsNowInsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> true
```

#### outside_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device event is outside of a given landmark, e.g.:

Fields:
```json
{
	"type": "outside landmark",
	"landmarkId": ""
}
```

Usage:
```go
	//create landmark
	tl, _ := geo.NewLongLat(-1, 1)
	tr, _ := geo.NewLongLat(1, 1)
	br, _ := geo.NewLongLat(1, -1)
	bl, _ := geo.NewLongLat(-1, -1)
	l = landmark.NewLandmark(*projID, "test", []*geo.Polygon{geo.NewPolygon([]*geo.LongLat{tl, tr, br, bl})})
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create the predicate
	p := InsideLandmark(l.ID.String())
	
	//evaluate the predicate under different scenarios
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> true
	p.Evaluate(eventIsNowOutsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> true
	p.Evaluate(eventIsNowInsideLandmark, deviceWasOutsideLandmark, lcs, gcs) --> false
	p.Evaluate(eventIsNowInsideLandmark, deviceWasInsideLandmark, lcs, gcs) --> false
```
   
#### landmark_has_all_tags

Description:
	This is a predicate that allows you to evaluate whether or not a given landmark has all of the tags specified, e.g.:

Fields:
```json
{
	"type": "landmark has all tags",
	"landmark_id": "",
	"tags": []
}
```

Usage:
```go
	//create the tags we are intersted in
	tags := []string{"foo", "bar"}

	//create landmark
	l = landmark.NewLandmark(*projID, "test", nil, tags...)
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create and evaluate the predicate under different scenarios
	LandmarkHasAllTags(l.ID, "ping", "pong").Evaluate(event, device, lcs, gcs) --> false
	LandmarkHasAllTags(l.ID, "ping", "bar").Evaluate(event, device, lcs, gcs) --> false
	LandmarkHasAllTags(l.ID, "foo", "pong").Evaluate(event, device, lcs, gcs) --> false
	LandmarkHasAllTags(l.ID, "foo", "bar").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "foo").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "bar").Evaluate(event, device, lcs, gcs) --> true
```

#### landmark_has_any_tags

Description:
	This is a predicate that allows you to evaluate whether or not a given landmark has any of the tags specified, e.g.:

Fields:
```json
{
	"type": "landmark has any tags",
	"landmark_Id": "",
	"tags": []
}
```

Usage:
```go
	//create the tags we are intersted in
	tags := []string{"foo", "bar"}

	//create landmark
	l = landmark.NewLandmark(*projID, "test", nil, tags...)
	
	//save landmark to cached landmark service
	lcs := landmark.CachedService(cache, service)
	lcs.Save(landmark.NewLandmark(*projID, "test", l))

	//create and evaluate the predicate under different scenarios
	LandmarkHasAllTags(l.ID, "ping", "pong").Evaluate(event, device, lcs, gcs) --> false
	LandmarkHasAllTags(l.ID, "ping", "bar").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "foo", "pong").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "foo", "bar").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "foo").Evaluate(event, device, lcs, gcs) --> true
	LandmarkHasAllTags(l.ID, "bar").Evaluate(event, device, lcs, gcs) --> true
```
   
#### event_occurred_after

Description:
	This is a predicate that allows you to evaluate whether an event occurred after a given time, e.g.:

Fields:
```json
{
	"type": "event occurred after",
	"moment": "2017-01-01T00:00:00Z",
}
```

Usage:
```go
//create the event and device we re intrested in
	d := &device.Device{}
	location1, _ := geo.NewLongLat(1, 1)
	location2, _ := geo.NewLongLat(-1, -1)
	timestamp1, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:00Z-08:00")
	timestamp2, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:05Z-08:00")
	eventLocation1 := geo.NewEventLocation(timestamp1.Unix(), *location1, 0, 0.5)
	eventLocation2 := geo.NewEventLocation(timestamp2.Unix(), *location2, 0, 0.5)
	eventLocations := []*geo.EventLocation{eventLocation1, eventLocation2}
	clientSentAt, _ := time.Parse(time.RFC3339, "2017-11-07T11:09:00-08:00")
	serverReceivedAt, _ := time.Parse(time.RFC3339, "2017-11-07T11:09:01-08:00")
	e := event.NewIotEvent(*ulid.New(), *ulid.New(), eventLocations, clientSentAt, serverReceivedAt, []byte{})
	
	//create and evaluate the predicate under different scenarios
	EventOccurredAfter(clientSentAt.Add(-time.Second)).Evaluate(e, d, lcs, gcs) --> true
	EventOccurredAfter(clientSentAt.Add(time.Second)).Evaluate(e, d, lcs, gcs) --> false
```

### event_occurred_before

Description:
	This is a predicate that allows you to evaluate whether an event occurred before a given time, e.g.:

Fields:
```json
{
	"type": "event occurred before",
	"moment": "2017-01-01T00:00:00Z",
}
```

Usage:
```go
//create the event and device we re intrested in
	d := &device.Device{}
	location1, _ := geo.NewLongLat(1, 1)
	location2, _ := geo.NewLongLat(-1, -1)
	timestamp1, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:00Z-08:00")
	timestamp2, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:05Z-08:00")
	eventLocation1 := geo.NewEventLocation(timestamp1.Unix(), *location1, 0, 0.5)
	eventLocation2 := geo.NewEventLocation(timestamp2.Unix(), *location2, 0, 0.5)
	eventLocations := []*geo.EventLocation{eventLocation1, eventLocation2}
	clientSentAt, _ := time.Parse(time.RFC3339, "2017-11-07T11:09:00-08:00")
	serverReceivedAt, _ := time.Parse(time.RFC3339, "2017-11-07T11:09:01-08:00")
	e := event.NewIotEvent(*ulid.New(), *ulid.New(), eventLocations, clientSentAt, serverReceivedAt, []byte{})
	
	//create and evaluate the predicate under different scenarios
	EventOccurredBefore(clientSentAt.Add(-time.Second)).Evaluate(e, d, lcs, gcs) --> false
 	EventOccurredBefore(clientSentAt.Add(time.Second)).Evaluate(e, d, lcs, gcs) --> true
```

#### event_occurred_between_times_of_day

Description:
	This is a predicate that allows you to evaluate whether an event occurred between two given times, e.g.:

Fields:
```json
{
	"type": "event occurred between times of day",
	"startInSecondsSinceMidnight": 0,
	"endInSecondsSinceMidnight": 0,
	"timeZone": "America/Los_Angeles"
}
```

Usage:
```go
	//create the event and device we re intrested in
	d := &device.Device{}
	location1, _ := geo.NewLongLat(1, 1)
	location2, _ := geo.NewLongLat(-1, -1)
	timestamp1, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:00Z-08:00")
	timestamp2, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:05Z-08:00")
	eventLocation1 := geo.NewEventLocation(timestamp1.Unix(), *location1, 0, 0.5)
	eventLocation2 := geo.NewEventLocation(timestamp2.Unix(), *location2, 0, 0.5)
	eventLocations := []*geo.EventLocation{eventLocation1, eventLocation2}
	clientSentAt, _ := time.Parse(time.RFC3339, "2017-11-07T12:00:00-08:00") //PST --> 15:00:00 EST
	serverReceivedAt, _ := time.Parse(time.RFC3339, "2017-11-07T12:00:00-08:00")
	e := event.NewIotEvent(*ulid.New(), *ulid.New(), eventLocations, clientSentAt, serverReceivedAt, []byte{})
	
	//create and evaluate the predicate under different scenarios
	EventOccurredBetweenTimesOfDay("11:59:59", "12:00:01").Evaluate(e, d, lcs, gcs) --> true
	EventOccurredBetweenTimesOfDay("12:00:01", "12:00:02").Evaluate(e, d, lcs, gcs) --> false
	EventOccurredBetweenTimesOfDay(14:59:59", "15:00:01", "America/New_York").Evaluate(e, d, lcs, gcs) --> true
	EventOccurredBetweenTimesOfDay("15:00:01", "15:00:02", "America/New_York").Evaluate(e, d, lcs, gcs) --> false
```

#### event_occurred_on_day_of_week

Description:
	This is a predicate that allows you to evaluate whether an event occurred on a given day of the week, e.g.:

Fields:
```json
{
	"type": "event occurred on day of week",
	"dayOfWeek": 0,
	"timeZone": "America/Los_Angeles"
}
```

Usage:
```go
	//create the event and device we re intrested in
	d := &device.Device{}
	location1, _ := geo.NewLongLat(1, 1)
	location2, _ := geo.NewLongLat(-1, -1)
	timestamp1, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:00Z-08:00")
	timestamp2, _ := time.Parse(time.RFC3339, "2017-11-17T12:00:05Z-08:00")
	eventLocation1 := geo.NewEventLocation(timestamp1.Unix(), *location1, 0, 0.5)
	eventLocation2 := geo.NewEventLocation(timestamp2.Unix(), *location2, 0, 0.5)
	eventLocations := []*geo.EventLocation{eventLocation1, eventLocation2}
	clientSentAt, _ := time.Parse(time.RFC3339, "2017-11-07T00:00:00-00:00") //Tuesday in UTC --> Monday in EST
	serverReceivedAt, _ := time.Parse(time.RFC3339, "2017-11-07T00:00:00-00:00")
	e := event.NewIotEvent(*ulid.New(), *ulid.New(), eventLocations, clientSentAt, serverReceivedAt, []byte{})
	
	//create and evaluate the predicate under different scenarios
	EventOccurredOnDayOfWeek(time.Tuesday).Evaluate(e, d, lcs, gcs) --> true
	EventOccurredOnDayOfWeek(time.Wedensday).Evaluate(e, d, lcs, gcs) --> false
	EventOccurredOnDayOfWeek(time.Monday).Evaluate(e, d, lcs, gcs) --> true
	EventOccurredOnDayOfWeek(time.Tuesday).Evaluate(e, d, lcs, gcs) --> false
```

## Rules

Rules define how the Sense Pipeline and Rules Engine respond to events that come from mobile and IoT devices. Rules are made up of condtion and actions pairs.  A rule can have one or more condition and actions pairs.  A rule loops through it's condition and actions pairs and returns the actions of the conditions that evaluate to true.  These are then passed on to action executors at a later stage. 

Fields:
```json
{
	"id": "",
	"created_at": "",
	"updated_at": "",
	"project_id": "",
	"name": "",
	"enabled": false,
	"conditionsAndActions": []
}
```

## Conditions and Actions

Conditions and actions describe what conditions must be met in order for the Rule to return some actions

Fields:
```json
	"condition": {}
	"actions": []
```

## Predicates

The rule's conditions are also known as predicates or logicals.  They are basically an object that has an evaluate method that returns true or false.

### and

Description:
	This is a boolean operator that allows you to combine other predicates with "AND" semantics, e.g.:

Fields:
```json
{
	"and": []
}
```

Logicals []Logical `json:"and,omitempty"`

Usage:
```
	And(p1, p2, ..., pN) --> p1 && p2 && ... && pN
```

### not

Description:
	This is a boolean operator that allows you to negate a predicate "NOT" semantics, e.g.:

Fields:
```json
{
	"not": []
}
```

Usage:
```go
	Not(p).Evaluate() --> !p
```

### or

Description:
	This is a boolean operator that allows you to combine other predicates with "AND" semantics, e.g.:

Fields:
```json
{
	"or": []
}
```

Usage:
```go
	Or(p1, p2, ..., pN).Evaluate() --> p1 || p2 || ... || pN
```

### always_false

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

### always_true

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
     
### event_free_form

Description:
	This is a predicate that allows you to evaluate an event field (usually an IoT event), e.g.:

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
     
### enter_landmark

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

### exit_landmark
   
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
	
### inside_landmark

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

### outside_landmark

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
   
### landmark_has_all_tags

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

### landmark_has_any_tags

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
   
### event_occurred_after

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

### event_occurred_between_times_of_day

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

### event_occurred_on_day_of_week

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

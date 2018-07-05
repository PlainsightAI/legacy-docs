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
		"subject": "Welcome to the gym!",
		"message": "Have a great workout"
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

## Actions

TODO

## Conditions
Conditions are composed of one or more predicates. Predicates can be joined with boolean operators to form a logical expression that can be evaluated.

### Operators

#### "and"

Description:
	This is a boolean operator that allows you to combine other predicates with "AND" semantics. In this example, a rule is checking for the specific device to be inside a landmark.

Example:
```json

	"logicalCondition": {
		"and": [{
			"type": "inside landmark",
			"landmarkId": "01C92NZT6WPQ0J87YVQDE72GGS"
		}, {
			"type": "event free form",
			"predicate": "deviceId == '01CGH777EWVP0E6EZZYH7P2JK0'"
		}]
	}
```

#### "not"

Description:
	This is a boolean operator that allows you to negate a predicate with "NOT" semantics. The following example uses "NOT" to include a schedule when the rule should not be triggered.

Example:
```json
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
  }
```

#### "or"

Description:
	This is a boolean operator that allows you to combine other predicates with "OR" semantics. In this example, the condition tests whether the device is in one landmark or the other.

Example:
```json

	"logicalCondition": {
		"or": [{
			"type": "inside landmark",
			"landmarkId": "01C85HTS8AG7WAYKW24WP74ZYA"
		}, {
			"type": "inside landmark",
			"landmarkId": "01C92NZT6WPQ0J87YVQDE72GGF"
		}]
	}
```
### Predicates
#### always_false

Description:
	This is a predicate that is always false.  It is mostly used for testing, e.g.:

Example:
```json
"logicalCondition": {
	"type": "always false"
}
```

#### always_true

Description:
	This is a predicate that is always true.  It is mostly used for testing, e.g.:


Example:
```json
"logicalCondition": {
	"type": "always true"
}
```
     
#### event_free_form

Description:
	This is a predicate that allows you to evaluate a specific event field in free-form fashion, typically used for IoT event payloads. This example triggers the rule when the tempF value in the payload is equal to a specific number.

Example:
```json
    "logicalCondition": {
 	"type": "event free form",
  	"predicate": "data.tempF == 67"
    }
```

#### enter_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device event entered a landmark that it was previously outside of. Landmarks are specific by the landmarkId. To specify multiple landmarks, you can join together multiple predicates with an operator.

Example:
```json
"logicalCondition": {
	"type": "enter landmark",
	"landmarkId": "01C92NZT6WCQ0J87YVQDE72GGA"
}
```

#### exit_landmark
   
Description:
	This is a predicate that allows you to evaluate whether or not a device exited a landmark that it was previously inside of. Landmarks are specific by the landmarkId. To specify multiple landmarks, you can join together multiple predicates with an operator.

Example:
```json
"logicalCondition": {
	"type": "exit landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGA"
}
```
	
#### inside_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device is inside of a given landmark. To specify multiple landmarks, you can join together multiple predicates with an operator.

Example:
```json
"logicalCondition": {
	"type": "inside landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGA"
}
```

#### outside_landmark

Description:
	This is a predicate that allows you to evaluate whether or not a device event is outside of a given landmark. To specify multiple landmarks, you can join together multiple predicates with an operator.

Example:
```json
"logicalCondition": {
	"type": "outside landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGJ"
}
```
   
#### landmark_has_all_tags

Description:
	This is a predicate that allows you to evaluate whether or not a given landmark has all of the tags specified. This example checks if the device has entered the landmark that it was not in previously AND if the landmark has both of the tags "music" and "has_wifi".

Example:
```json
{
      "and": [{
        "type": "landmark has all tags",
        "landmark_id": "01CHNRNYKM4SH9EPNRJC62H1PJ",
        "tags": ["music", "has_wifi"]
      },{
        "type": "enter landmark",
        "landmarkId": "01CHNRNYKM4SH9EPNRJC62H1PJ"
      }] 
}
```

#### landmark_has_any_tags

Description:
	This is a predicate that allows you to evaluate whether or not a given landmark has any of the tags specified. This example checks if the device is inside the landmark AND if the landmark has either of the tags "music" or "has_wifi".

Example:
```json
"logicalCondition": {
      "and": [{
        "type": "landmark has any tags",
        "landmark_id": "01CHNRNYKM4SH9EPNRJC62H1PJ",
        "tags": ["music", "has_wifi"]
      },{
        "type": "inside landmark",
        "landmarkId": "01CHNRNYKM4SH9EPNRJC62H1PJ"
      }] 
}
```
   
#### event_occurred_after

Description:
	This is a predicate that allows you to evaluate whether an event occurred after a given time. This example checks if the event is timestamp after 2017-01-01T00:00:00Z 

Example:
```json
"logicalCondition": {
	"type": "event occurred after",
	"moment": "2017-01-01T00:00:00Z",
}
```

### event_occurred_before

Description:
	This is a predicate that allows you to evaluate whether an event occurred before a given time. This example checks if the event is timestamped before 2017-01-01T00:00:00Z

Example:
```json
"logicalCondition": {
	"type": "event occurred before",
	"moment": "2017-01-01T00:00:00Z",
}
```

#### event_occurred_between_times_of_day

Description:
	This is a predicate that allows you to evaluate whether an event occurred between two given times. This example checks if the event is timestamped between these hours of the day. TODO

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
	This is a predicate that allows you to evaluate whether an event occurred on a given day of the week, e.g.: TODO

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

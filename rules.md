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
	"enabled": true
}
```
Fields:
* name - (required) Name of the rule
* description - (optional) Description of rule
* throttleInSeconds - (optional) Specifies the minimum amount of time between executing the action for this rule (in seconds). For example, if set to 300 the action will be executed no more than once every 5 minutes. Defaults to 0
* actions - Describes what will occur when the condition is satisfied. See [Actions](#actions) for more details.
* logicalCondition - (optional) Describes the conditions that must be met in order to execute the action(s). A condition can be composed of one or more predicates joined with boolean operators. See [Conditions](#conditions) for more details.
* enabled - The state of the rule. If true, the rule is actively running; if false, it is disabled.

## Actions

Rules triggers one or more actions when an event occurs. Available actions include sending an SMS, a push notification to a device, sending an email, or invoking a webhook. 

### push
Sends a push notification to the triggering mobile device. *Requires a mobile app using the Reach SDK.*

Fields:
* type - "push"
* subject - (string) the subject content of the push message
* message - (string) the body of the push message. [Templates](http://docs.sixgill.com/templates.html) can be used which will be replaced dynamically when the rule is triggered

Example:
```json
"actions": [{
        "type": "push",
        "subject": "Welcome to the gym!",
        "message": "Have a great workout"
}]
```

### email
Sends an email to one or more recipients when the rule is triggered.

Fields:
* type - "email"
* subject - (string) the subject content of the email
* message - (string) the body of the email. [Templates](http://docs.sixgill.com/templates.html) can be used which will be replaced dynamically when the rule is triggered
* recipients - an object specifying an array of email addresses to send to when the rule is triggered

Example:
```json
"actions":[{
  	"type": "email",
  	"subject": "entering gym",
  	"message": "have a great workout",
  	"recipients": {
  		"emails": ["test@test.com"]
	}
}]
```

### webhook 
Invokes a webhook to a web service. This is typically used to send HTTP requests to external systems when a rule is triggered.

Fields:

* type - "webhook"
* method - (required) POST, PUT, PATCH, DELETE
* headers - (optional) An object specifying the request headers. See example below.
* url - (required) the URL of the request. [Templates](http://docs.sixgill.com/templates.html) can be used which will be replaced dynamically when the rule is triggered
* body - (optional) the request payload. [Templates](http://docs.sixgill.com/templates.html) can be used which will be replaced dynamically when the rule is triggered
* username - (optional) username for authorization
* password - (optional) password for authorization

> The following example sends a POST request to https://sixgill.com with a simple JSON payload. 

Example:
```json
"actions":[{ 
         "type":"webhook",
         "method":"POST",
         "headers":{ 
            "header":"Content-Type",
            "value":"text/json"
         },
         "url":"https://sixgill.com",
         "body":"{\"test\": \"test\"}",
         "username":"username",
         "password":"password"
}]
```

### SMS
Sends an SMS to the specified recipient telephone numbers.

Fields:
* type - "sms"
* message - (string) the content of the SMS message
 * recipients - an object specifying an array of telephone phone numbers to send to when the rule is triggered

Example:
```json
"actions": [{
	"type": "sms",
	"message": "test",
	"recipients": {
		"telephoneNumbers": ["5853144015"]
	}
}]
```   

## Conditions
Conditions are composed of one or more predicates. Predicates can be joined with boolean operators to form a logical expression that can be evaluated.

### Operators

#### "and"

Description:
	This is a boolean operator that allows you to combine other predicates with "AND" semantics. All predicates combined with "AND" must evaluate to true for the condition to be met.
	
> The following example checks for the specific device to be inside a landmark.

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
	This is a boolean operator that allows you to negate a predicate with "NOT" semantics. The predicate must not evaluate to true for the condition to be met.

> The following example uses "NOT" to include a schedule of when the rule should not be triggered.

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
	This is a boolean operator that allows you to combine other predicates with "OR" semantics. Either predicate must evaluate to true for the condition to be met.

> The following condition checks whether the device is inside one landmark or the other.

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
	This predicate is always false.  It is typically used for testing.

Example:
```json
"logicalCondition": {
	"type": "always false"
}
```

#### always_true

Description:
	This predicate is always true.  It is typically used for testing.


Example:
```json
"logicalCondition": {
	"type": "always true"
}
```
     
#### event_free_form

Description:
	This predicate allows you to evaluate a specific event field in free-form fashion, typically used for IoT event payloads. 

Fields:
* type - "event free form"
* predicate - String representing the freeform equation to be evaluated. This is typically construcgted off of the event payload that will be received for the device. 

> The following example triggers the rule when the data.tempF value in the event payload is equal to 67.
Example:
```json
"logicalCondition": {
 	"type": "event free form",
 	"predicate": "data.tempF == 67"
 }
```

> An example payload that would trigger the above condition:

```json
"data":{
     "tempF":67,
     "humidity":23
}
```

#### enter_landmark

Description:
	This is predicate allows you to evaluate whether or not a device event entered a landmark that it was previously outside of. Landmarks are specific by the landmarkId. To specify multiple landmarks, you can join together multiple predicates with an operator.

Fields:
* type - "enter landmark"
* landmarkId - ID of the landmark to compare the device's location against

> The following example checks if the device was previously outside the landmark 01C92NZT6WCQ0J87YVQDE72GGA and is now inside it.

Example:
```json
"logicalCondition": {
	"type": "enter landmark",
	"landmarkId": "01C92NZT6WCQ0J87YVQDE72GGA"
}
```

#### exit_landmark
   
Description:
	This predicate allows you to evaluate whether or not a device exited a landmark that it was previously inside of. To specify multiple landmarks, you can join together multiple predicates with an operator.

Fields:
* type - "exit landmark"
* landmarkId - ID of the landmark to compare the device's location against

> The following example checks if the device was previously inside the landmark and is now outside of it.

Example:
```json
"logicalCondition": {
	"type": "exit landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGA"
}
```
	
#### inside_landmark

Description:
	This predicate allows you to evaluate whether or not a device is inside of a given landmark. To specify multiple landmarks, you can join together multiple predicates with an operator.

Fields:
* type - "inside landmark"
* landmarkId - ID of the landmark to compare the device's location against

> The following example checks if the device is inside the landmark.

Example:
```json
"logicalCondition": {
	"type": "inside landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGA"
}
```

#### outside_landmark

Description:
	This predicate allows you to evaluate whether or not a device event is outside of a given landmark. To specify multiple landmarks, you can join together multiple predicates with an operator.

Fields:
* type - "outside landmark"
* landmarkId - ID of the landmark to compare the device's location against

> The following example checks if the device is outside the landmark.

Example:
```json
"logicalCondition": {
	"type": "outside landmark",
	"landmarkId": "01C92NZTCWPQ0J87YVQDE72GGJ"
}
```
   
#### landmark_has_all_tags

Description:
	This predicate allows you to evaluate whether or not a given landmark has all of the tags specified. 

Fields:
* type - "landmark has all tags"
* landmark_id - the ID of the landmark whose tags should be checked
* tags - an array of strings representing the tags to be checked for this landmark

> The following example checks if the device has entered the landmark that it was not in previously AND if the landmark has both of the tags "music" and "has_wifi" assigned to it.

Example:
```json
"logicalCondition": {
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
	This predicate allows you to evaluate whether or not a given landmark has any of the tags specified.
	
Fields:
* type - "landmark has any tags"
* landmark_id - the ID of the landmark whose tags should be checked
* tags - an array of strings representing the tags to be checked for this landmark

> The following example checks if the device is inside the landmark AND if the landmark specified has either of the "music" or "has_wifi" tags assigned to it.
 
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
	This predicate allows you to evaluate whether an event occurred after a given time.

Fields:
* type = "event occurred after"
* moment - timestamp in ISO8601 format that will be used for comparison against the event's timestamp

> The following example checks if the event is timestamped after 2017-01-01T00:00:00Z 

Example:
```json
"logicalCondition": {
	"type": "event occurred after",
	"moment": "2017-01-01T00:00:00Z",
}
```

### event_occurred_before

Description:
	This predicate allows you to evaluate whether an event occurred before a given time.
	
Fields:
* type - "event occurred before"
* moment - timestamp in ISO8601 format that will be used for comparison against the event's timestamp

> The following example checks if the event is timestamped before 2017-01-01T00:00:00Z

Example:
```json
"logicalCondition": {
	"type": "event occurred before",
	"moment": "2017-01-01T00:00:00Z",
}
```

#### event_occurred_between_times_of_day

Description:
	This is a predicate that allows you to evaluate whether an event occurred between two given times. 

Fields:
* type - "event occurred between times of day",
* startInSecondsSinceMidnight - Start time in seconds since midnight
* endInSecondsSinceMidnight - End time in seconds since midnight
* An identifier from the IANA Timezone Database which identifies the timezone that should be used for the schedule 

> The following example checks if the event is timestamped between 4:00pm and 6:00pm in the Detroit timezone.

Example:
```json
"logicalCondition": {
	"type": "event occurred between times of day",
	"startInSecondsSinceMidnight": 57600,
	"endInSecondsSinceMidnight": 64800,
	"timeZone": "America/Detroit"
}
```

#### event_occurred_on_day_of_week

Description:
	This predicate allows you to evaluate whether an event occurred on a given day of the week.

Fields:
* type - "event occurred on day of week"
* dayOfWeek - The integer representing the day of the week (0 = Sunday, 1 = Monday, 2 = Tuesday, etc)
* timeZone - An identifier from the IANA Timezone Database which identifies the timezone that should be used for the schedule

> The following example checks if the event occurred on a specific day of the week.

Example:
```json
"logicalCondition": {
	"type": "event occurred on day of week",
	"dayOfWeek": 0,
	"timeZone": "America/Los_Angeles"
}
```


# Templates

## Introduction

Templates provide a way for emails, sms, and push messages to be dynamic.  For example lets say you want to send a slightly different sms based on whats OS a device is running on.  The message could be

```
{% raw %}
Welcome to the airport.  Download our app for its cool features:
{{if eq .Device.OS "iOS"}}
https://itunes.apple.com/us/app/american-airlines/id382698565?mt=8
{{else if eq .Device.OS "Android"}}
https://play.google.com/store/apps/details?id=com.aa.android&hl=en
{{end}}
{% endraw %}
```

## Template Language

Sixgill's templates rely on Go's (Golang) template language syntax.  It is fully documented at: https://golang.org/pkg/text/template

## Template Data Model

The Template Data Model is the data fed into the text of the template to be rendered.  Here is an outline of all the accessible fields:

### .Device

Field|Type|Description
-|-|-
CreatedAt | time.Time | When the device was created
UpdatedAt | time.Time | When the device was update
Manufacturer | string | Name of the manufacturer
Model | string | Name of the device model
OS | string | Name of the device OS
OSVersion | string | Name of the device OS version
SoftwareVersion | string | Name of the Sixgill software version
Type | string | Type of device (android,ios,iot)
Sensors | string | Comma seperated list of available sensors
State | DeviceState | State of the Device

### .Device.State

Field|Type|Description
-|-|-
Tags | [string] | Array of string tags
Location | LongLat | The last known location of the device
LastUpdatedAt | time.Time | When the device state was updated
Payload | key value | Key value data associated with the device state

### .Device.State.Location

Field|Type|Description
-|-|-
Latitude | float64 | Latitude
Longitude | float64 | Longitude

### .Channel

Field|Type|Description
-|-|-
Name | string | Name of the channel
Type | string | Type of the channel

### .Event

Field|Type|Description
-|-|-
ClientSentAt | time.Time | When the event was sent from the device
ServerReceivedAt | time.Time | When the event was recieved by the server
Locations | [EventLocation] | Locations for the event
Wifis | [Wifi] | Wifis for the event
Beacons | [Beacon] | Beacons for the event
Power | [Power] | Power readings for the event
Activities | [Activity] | Activities for the event
Attributes | key value | Key value data for the event

### .Event.Locations

Field|Type|Description
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds
Latitude | float64 | Latitude
Longitude | float64 | Longitude
Velocity | float64 | Velocity in meters / sec
Accuracy | float64 | Accuracy of the reported latitude and longitude in meters

### .Event.Wifis

Field|Type|Description
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds
MAC | string | MAC address
SSID | string | SSID
RSSI | int | RSSI

### .Event.Beacons

Field|Type|Description
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds
MAC | string | MAC address
RSSI | int | RSSI
Major | int | BLE major identifier
Minor | int | BLE minor identifier
Proximity | string | BLE proximity field
Accuracy | float64 | Accuracy of the reported proximit

### .Event.Power

Field|Type|Description
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds
Charging | bool | If the device is charging
BatteryLevel | int | Battery level as a percent

### .Event.Activities

Key value data
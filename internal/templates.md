# Templates

## Introduction

Sixgill uses [Go Templates](https://golang.org/pkg/html/template/) to let users make dynamic emails, sms, and push messages.  

For example, you may want to send a different sms to devices with different Operating Systems.

```
{% raw %}
Welcome to the JFK Airport. Download our app to take advantage of its awesome features:
{{if eq .Device.OS "iOS"}}
https://itunes.apple.com/us/app/american-airlines/id382698565?mt=8
{{else if eq .Device.OS "Android"}}
https://play.google.com/store/apps/details?id=com.aa.android&hl=en
{{end}}
{% endraw %}
```

Or you might want to send a device's location as JSON to a device-specific url:

```
{% raw %}
http://username:password@host/Thingworx/Things/{{.Device.ID.String}}/Properties/Location
{% endraw %}
```

```
{% raw %}
{
    "Location": {
        "latitude":{{.Device.State.CurrLocation.Latitude}},
        "longitude":{{.Device.State.CurrLocation.Longitude}}
    }
}
{% endraw %}
```

## Template Data Model

The Template Data Model is the structured data that's fed into the templates. Here is an outline of all the model's accessible fields:

### .Device

Field|Type|Description|Usage
-|-|-
ID | ULID | Unique Identifier (.Device.ID.String to get string value) | .Device.ID.String
CreatedAt | time.Time | When the device was created | .Device.CreatedAt
UpdatedAt | time.Time | When the device was update | .Device.UpdatedAt
Manufacturer | string | Name of the manufacturer | .Device.Manufacturer
Model | string | Name of the device model | .Device.Model
OS | string | Name of the device OS | .Device.OS
OSVersion | string | Name of the device OS version | .Device.OSVersion
SoftwareVersion | string | Name of the Sixgill software version | .Device.SoftwareVersion
Type | string | Type of device (android,ios,iot) | .Device.Type
Sensors | string | Comma seperated list of available sensors | .Device.Sensors
State | DeviceState | State of the Device | .Device.State

### .Device.State

Field|Type|Description|Usage
-|-|-
Tags | [string] | Array of string tags | .Device.State.Tags
Location | LongLat | The last known location of the device | .Device.State.Location
LastUpdatedAt | time.Time | When the device state was updated | .Device.State.LastUpdatedAt
Payload | key value | Key value data associated with the device state | .Device.State.Payload

### .Device.State.CurrLocation

Field|Type|Description|Usage
-|-|-
Latitude | float64 | Latitude | .Device.State.CurrLocation.Latitude
Longitude | float64 | Longitude | .Device.State.CurrLocation.Longitude

### .Channel

Field|Type|Description|Usage
-|-|-
Name | string | Name of the channel | .Channel.Name
Type | string | Type of the channel | .Channel.Type

### .Event

Field|Type|Description|Usage
-|-|-
ClientSentAt | time.Time | When the event was sent from the device | .Event.ClientSentAt
ServerReceivedAt | time.Time | When the event was received by the server | .Event.ServerReceivedAt
Locations | [EventLocation] | Locations for the event | .Event.Locations
Wifis | [Wifi] | Wifis for the event | .Event.Wifis
Beacons | [Beacon] | Beacons for the event | .Event.Beacons
Power | [Power] | Power readings for the event | .Event.Power
Activities | [Activity] | Activities for the event | .Event.Activities
Attributes | key value | Key value data for the event | .Event.Attributes

### .Event.Locations

Field|Type|Description|Usage
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds | (index .Event.Locations 0).Timestamp
Latitude | float64 | Latitude | (index .Event.Locations 0).Latitude
Longitude | float64 | Longitude | (index .Event.Locations 0).Longitude
Velocity | float64 | Velocity in meters / sec | (index .Event.Locations 0).Velocity
Accuracy | float64 | Accuracy of the reported latitude and longitude in meters | (index .Event.Locations 0).Accuracy

### .Event.Wifis

Field|Type|Description|Usage
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds | (index .Event.Wifis 0).Timestamp
MAC | string | MAC address | (index .Event.Wifis 0).MAC
SSID | string | SSID | (index .Event.Wifis 0).SSID
RSSI | int | RSSI | (index .Event.Wifis 0).RSSI

### .Event.Beacons

Field|Type|Description|Usage
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds | (index .Event.Beacons 0).Timestamp
MAC | string | MAC address | (index .Event.Beacons 0).MAC
RSSI | int | RSSI | (index .Event.Beacons 0).RSSI
Major | int | BLE major identifier | (index .Event.Beacons 0).Major
Minor | int | BLE minor identifier | (index .Event.Beacons 0).Minor
Proximity | string | BLE proximity field | (index .Event.Beacons 0).Proximity
Accuracy | float64 | Accuracy of the reported proximity | (index .Event.Beacons 0).Accuracy

### .Event.Power

Field|Type|Description|Usage
-|-|-
Timestamp | int64 | Unix timestamp in milliseconds | (index .Event.Power 0).Timestamp
Charging | bool | If the device is charging | (index .Event.Power 0).Charging
BatteryLevel | int | Battery level as a percent | (index .Event.Power 0).BatteryLevel

### .Event.Activities

Key value data

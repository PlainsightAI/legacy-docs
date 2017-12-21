# Models

## Mobile

### Event

Events are sensor collection payloads whose configuration, properties and attributes values can be updated.

Field|Type|Description
-|-|-
locations | [Location] | Set of location readings
wifis | [Wifi] | Set of wifi readings
ibeacons | [iBeacon] | Set of iBeacon readings
power | [Power] | Set of power readings
activities | [Activity] | Set of activities (walking, still, driving, etc.)
configuration | Configuration | Configuration (cadence and sdk settings) at the time of the message
properties | Properties | Properties (hardware and software info) at the time of the message
attributes | key-value | Developer set metadata about device at the time of the message

### Wifi

Wifis are detected Wifi access points.

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds
mac | string | mac address of access point
ssid | string | ssid of access point
rssi | int | rssi value of signal strength

### iBeacon

iBeacons are detected access points that support the iBeacon BLE protocol.  

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds
uuid | string | iBeacon UUID
rssi | int | rssi value of signal strength
major | int | iBeacon major value
minor | int | iBeacon minor value
proximity | string | unknown, immediate, near, or far
accuracy | double | accuracy in meters

### Power

Power is the battery reading from a device.

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds
charging | boolean | whether or not the device is charging
batteryLevel | int | battery percentage of full

### Activity

Activity is a general reading of a device's velocity.

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds
type | string | unknown, still, walking, running, in-vehicle, or on-bicycle
confidenceLevel | string | low, medium, or high
confidencePercentage | int | confidence as a percentage

### Configuration

Configuration is a set of values that can alter a device's SDK's functionality.  

Field|Type|Description
-|-|-
enabled | boolean | whether or not the SDK is functioning
cadence | int | duration in milliseconds between event collection periods
sensors | [string] | whitelist of sensors to use for event collection
eventTTL | int | duration in milliseconds to keep events locally after creation
maxStorage | int | storeage size in bytes of local event cache

### Properties

The properties object is a collection of general information on a device's hardware and software components.

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds change was detected
manufacturer | string | Hardware manufacturer
model | string | Hardware model
os | string | OS type (Android, iOS, Linux, etc.)
osVersion | string | OS Version
softwareVersion | string | SDK version in use
type | string | Internal device type (Android, iOS, IoT)
attributes | key-values | Key value pairs set by device
sensors | [string]| Sensors available on device

### Push

Push is an overview of an event's Android or iOS push notification information.

Field|Type|Description
-|-|-
timestamp | int | unix timestamp in milliseconds change was detected
token | string | token used in conjunction with type to send push notifications
type | string | ios-development, ios-production, android-gcm, android-fcm, or android-c2dm

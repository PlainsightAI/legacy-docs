# Sixgill Reach iOS SDK Setup
The Sixgill Reach iOS SDK v2 is a package for collecting iOS device sensor data for use with the Sixgill Sense platform. In order to fully utilize the Reach SDK, permissions will have to be requested at app level to enable features using Core Location, Push Notifications, and Core Motion. Follow the guides below to configure your app.

## Installation
To integrate Sixgill into your Xcode project use CocoaPods

### CocoaPods
CocoaPods is a dependency manager for Cocoa projects. You can install it with the following command:
```
$ gem install cocoapods
```

Specify following in your ```Podfile```
```
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/sixgill/SixgillPodSpecs.git'

platform :ios, '9.0'
use_frameworks!

target '<Your Target Name>' do
pod 'SixgillSDK'
end
```

Then, run the following command:
```
$ pod install
```

## SDK Configuration

The Sixgill Reach SDK is configurable to suit a wide variety of use cases. The SDK comes with preconfigured defaults that are acceptable for most cases though not optimal for all. The configurations and the default settings are detailed below:

1. Bluetooth Beacon Collection Span: Every time the device is required to report its current location, it attempts to find any bluetooth iBeacons within range. Since beacon signals aren't entirely reliable, this configuration allows app developers to specify how long to consider a beacon signal is valid before disregarding it. This configuration is measured in seconds. The default value is 5 seconds.

2. Location Collection Span: The Reach SDK reports the most accurate location possible back to the cloud. The accuracy of the onboard GPS (Core Location) services varies with collection time. A GPS fix gathered after 3 seconds will likely be more accurate than a fix gathered after 7 seconds. The default is 3 seconds.

3. Location Collection Queue Policy: The Reach offline location cache is configurable with one of three different policies:

    1. **SGMSQueuePolicyDefault**: Works like a queue. First in, Last out.
    2. **SGMSQueuePolicyJumpQueue**: A modified queue that prioritizes the last reported location. FILO except the last location is first
    3. **SGMSQueuePolicyLastUpdate**: This setting only stores the last reported update and does not cache anything else.



## Permission Configuration

### Enabling Background Mode Capabilities

1. Go the Capabilities tab for your app's target and enable "Background Modes"
1. Check "Location Updates", "Background fetch", and "Remote notifications"

### Core Location

1. Find the UIRequiredDeviceCapabilities key in your app's Info.plist file and add the following keys to the array:
   - location-services
   - gps
2. In your app's Info.plist file, add the keys  `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` with string values. Set the string values to the messages you want to be displayed to your users when your app requests permission to use the location services. An example would be something like `Weather uses your location to provide you with accurate forecasts wherever you go.`
3. If your app supports iOS 10 and earlier, also add the key `NSLocationAlwaysUsageDescription` with a string value. Set the string value with message similar to above.

After you've followed the above steps, you are ready to request permission from the user to use location services using the SGDK's requestLocationPermission call:

```
_locationManager.delegate = self;
[SGSDK requestAlwaysPermission];
```

### Push Notifications

Navigate to App Settings > Capabilities and switch Push Notifications to On. Confirm that push notifications has been enabled by checking the App Bundle ID on your Apple Developer account.

### Core Motion

In your app's Info.plist file add the key `NSMotionUsageDescription` with a string value. Set the string value to the message you want to be displayed to your users when your app requests permission to use the core motion services.

## Usage

Using the Reach SDK is simple. Once initialized and enabled, the SDK will run in the background.


### SDK Initialization

In your app delegate's `didFinishLaunchingWithOptions` method add the following code:

```objc
[SGSDK initWithAPIKey:"YOUR_API_KEY"];
[SGSDK enable];
[SGSDK setMotionActivityEnabled:YES]; // Depending on if you need activity services
```

### SDK Stop

In your app, when you want to stop the SDK, add the following code:

```objc
[SGSDK disable];
```

### Integrating Push Notifications

First check you have `Push Notification` in `Permission Configuration`, then go ahead with following steps:

To register for push notifications, add the following in app delegate's `didFinishLaunchingWithOptions` method:
```objc
[[UIApplication sharedApplication] registerForRemoteNotifications];
```

To hook the Reach SDK up to your app's push notification, implement the following code in your app delegate's `didReceiveRemoteNotification` method.
```objc
[SGSDK didReceivePushNotificationPayload:userInfo withCompletionHandler:completionHandler];
```

In your app delegate's `didRegisterForRemoteNotificationsWithDeviceToken` method add the following code to register device token:
```objc
const unsigned *tokenBytes = [deviceToken bytes];
NSString *hexToken = [NSString stringWithFormat:@"%08x%08x%08x%08x%08x%08x%08x%08x",
ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
[SGSDK setPushToken:hexToken];
```

### SDK functonalities
Reach provides following methods to expose it's dfferent functionalities-

To start Reach sensors:
```objc
[SGSDK enable];
```

To stop Reach sensors:
```objc
[SGSDK disable];
```

To get Reach logs:
```objc
[SGSDK logs];
```

To clear Reach logs:
```objc
[SGSDK clearLogs];
```

To get Sixgill Device ID:
```objc
[SGSDK deviceId];
```

To force sensors update:
```objc
[SGSDK forceSensorUpdate];
```

To get sensor update event location:
```
NSArray *locations = [SGSDK sensorUpdateHistory:count];

for (NSDictionary *d in locations) {
    NSNumber *lat = d[@"lat"];
    NSNumber *lon = d[@"lon"];
    float laf = lat.floatValue;
    float lof = lon.floatValue;
}
```

To get the sensor updates:

- In class where you want this data, extend that class with  `SensorUpdateDelegate`.
- On viewDidAppear, do this
```objc
[SGSDK registerForSensorUpdates:self];
```
- On viewWillDisappear, do this
```objc
[SGSDK registerForSensorUpdates:nil];
```
- Implement this method `sensorUpdateSentWithData`  to receive latest `Ingress.Event` from the SDK. `Ingress.Event` contains different sensors data, it can be implement as:
```
- (void)sensorUpdateSentWithData:(Event *)sensorData { 
}
```

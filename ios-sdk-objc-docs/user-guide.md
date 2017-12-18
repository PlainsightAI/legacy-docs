# Sixgill Reach iOS SDK Setup
The Sixgill Reach iOS SDK v2 is a package for collecting iOS device sensor data for use with the Sixgill Sense platform. In order to fully utilize the Reach SDK, permissions will have to be requested at app level to enable features using Core Location, Push Notifications, and Core Motion. Follow the guides below to configure your app.

## SDK Configuration

The Sixgill Reach SDK is configurable to suit a wide variety of use cases. The SDK comes with preconfigured defaults that are acceptable for most cases though not optimal for all. The configurations and the default settings are detailed below:

1. Bluetooth Beacon Collection Span: Every time the device is required to report its current location, it attempts to find any bluetooth iBeacons within range. Since beacon signals aren't entirely reliable, this configuration allows app developers to specify how long to consider a beacon signal is valid before disregarding it. This configuration is measured in seconds. The default value is 5 seconds.

2. Location Collection Span: The Reach SDK reports the most accurate location possible back to the cloud. The accuracy of the onboard GPS (Core Location) services varies with collection time. A GPS fix gathered after 7 seconds will likely be more accurate than a fix gathered after 3 seconds. The default is 7 seconds.

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
2. In your app's Info.plist file add the key `NSLocationAlwaysUsageDescription` with a string value. Set the string value to the message you want to be displayed to your users when your app requests permission to use the location services. An example would be something like `Weather uses your location to provide you with accurate forecasts wherever you go.`

After you've followed those two steps, you are ready to request permission from the user to use location services using the SGDK's requestLocationPermission call:

`[SGSDK requestLocationPermission];`

### Push Notifications

Navigate to App Settings > Capabilities and switch Push Notifications to On. Confirm that push notifications has been enabled by checking the App Bundle ID on your Apple Developer account.

To hook the Reach SDK up to your app's push notification, implement the following call in your app delegate's `didReceiveRemoteNotification` method.
```objc
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler{
    
    [SGSDK didReceivePushNotificationPayload:userInfo withCompletionHandler:completionHandler];
    
}
```

### Core Motion

In your app's Info.plist file add the key `NSMotionUsageDescription` with a string value. Set the string value to the message you want to be displayed to your users when your app requests permission to use the core motion services.

## SDK Initialization

In your app delegate's `didFinishLaunchingWithOptions` method add the following code:

```objc
[SGSDK init];
[SGSDK setMotionActivityEnabled:YES]; // Depending on if you need activity services
```
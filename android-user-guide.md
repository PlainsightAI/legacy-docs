# Sixgill Reach Android SDK Setup
The Sixgill Reach Android SDK is a package for collecting android device sensor data for use with the Sixgill Sense platform. In order to fully utilize the Reach SDK, permissions will have to be requested at app level to enable features using Location, Push Notifications, and Wifi Sensors. The SDK is "plug and play" and only requires configuration. Follow the guides below to configure your app.

## Installation

~~Sixgill's Reach SDK can be installed using Gradle or~~ manually downloading and including an Android Archive.   

~~**Gradle**~~

~~Add the following to your app's build.gradle file:~~

```
compile 'com.sixgill.reach:reach:1.0.0'
```

**Manual**

~~Download the [latest Reach Android Archive](https://assets.sixgill.com/sdk/android/1.0.0.aar) and [integrate it into your project](https://developer.android.com/studio/projects/android-library.html#AddDependency).~~


## Configuration

**Permissions**

Some of these permissions are requirements, but some can be optionally omitted to disable specific sensors.  

| Permission | Required | Omission Effect |
| --- | --- | --- |
| ACCESS_FINE_LOCATION | Yes | SDK inactive |
| ACCESS_COARSE_LOCATION | Yes | SDK inactive |
| ACCESS_WIFI_STATE | No | Disable Wifi detection|
| BLUETOOTH | No | Disable Beacon detection|
| BLUETOOTH_ADMIN | No | Disable Beacon detection|
| INTERNET | Yes | SDK inactive |
| ACTIVITY_RECOGNITION | No | Disable User Activity detection |

For Android platforms that require Runtime Permissions, it is up to the developer using the Reach SDK to get all required permissions from user depending on which sensors are being used.
If permissions are not requested or not granted by the user, the specific sensors will collect any data.
Permissions required by SDK are:
```xml
<!--Location sensors-->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-feature
        android:name="android.hardware.location.gps"
        android:required="false"/><!--optionally tell the device that app uses GPS, but it is not required-->

<!--Beacons sensors-->
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
    <uses-feature
        android:name="android.hardware.bluetooth_le"
        android:required="false"/><!--optionally tell the device that app uses Bluetooth LE sensors, but it is not required-->

<!--Wifi sensors-->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-feature
        android:name="android.hardware.wifi"
        android:required="true"/><!--tell the device that app uses wifi hardware, which is required-->

<!--Activity recognition sensors-->
    <uses-permission android:name="com.google.android.gms.permission.ACTIVITY_RECOGNITION"/>

<!--other required sensors-->
    <uses-feature android:name="android.hardware.sensor.accelerometer"
                  android:required="true" />
    <uses-feature android:name="android.hardware.sensor.compass"
                  android:required="true" />
    <uses-feature android:name="android.hardware.sensor.gyroscope"
                  android:required="true" />
```

On devices that require runtime permissions request and acquire required permissions from user before starting Reach sdk.

> For more info see [Requesting runtime permissions](https://developer.android.com/training/permissions/requesting.html)

## Usage
Using the Reach SDK is simple.  Once initialized and enabled, the SDK will run in the background.

### SDK Initialization

For Sixgill hosted applications, add this to your Application class `onCreate` lifecycle method:

```java
/**
* @param context {@link Context}
* @param apiKey {@link String}
* @return void
*/
Reach.initWithAPIKey(context, "YOUR_API_KEY");
```

```java

public class MainApplication extends Application {
    public void onCreate() {
        super.onCreate();
        Reach.initWithAPIKey(this,"YOUR_API_KEY");
    }
}
```
> Note: `initWithAPIKey` must be called in Application instance class before using Reach SDK at all

### Choosing Providers

Reach offers some 3rd party provider options for indoors positioning.
To enable a provider you have to explicitly set *Location Provider* before calling `Reach.enable`
Currently there are two providers available, Indoors and IndoorAtlas. Choosing a provider to run is completely optional.
For scenarios where a provider is not able to give user position, Reach fallbacks to GPS positioning.

```java
public class MainApplication extends Application {
    public IReachProvider mIndoorsProvider;
    public IReachProvider mIndoorAtlasProvider;
    public void onCreate() {
        super.onCreate();
        mIndoorsProvider = new IndoorsProvider();
        mIndoorAtlasProvider = new IndoorAtlasProvider();
        Reach.initWithAPIKey(this,"YOUR_API_KEY");
        Reach.setLocationProvider(mIndoorsProvider, this); /* OR */ Reach.setLocationProvider(mIndoorAtlasProvider, this);
    }

    ...

    @Override
    public void onTerminate() {
        super.onTerminate();
        Reach.releaseLocationProvider(mIndoorsProvider, this); /* OR */ Reach.releaseLocationProvider(mIndoorAtlasProvider, this);
    }
}
```
> Note: choosing a provider is completely optional not a mandatory step

**You can only use one provider at a time. The provider set later will auto replace the previous one.**
**To prevent memory leaks, always release location provider in *Application* class *onTerminate* lifecycle.**

### Reach functonalities
Other than `initWithAPIKey`, `setLocationProvider` and `releaseLocationProvider` mentioned above, Reach provides following methods to expose it's dfferent functionalities-

To start Reach sensors, call `enable` passing a `Context`.

```java
/**
* @param context {@link Context}
* @return void
*/
Reach.enable(context);
```

To stop Reach sensors, call `disable` passing a `Context`.

```java
/**
* @param context {@link Context}
* @return void
*/
Reach.disable(context);
```

To get Sixgill Device ID:

```java
/**
* @param context {@link Context}
* @return String - SixGill DeviceId
*/
Reach.deviceId(context)
```

To get Reach logs:
```java
/**
* @param context {@link Context}
* @return String - stored logs from SDK
*/
Reach.logs(context)
```

To clear Reach logs:
```java
/**
* @param context {@link Context}
* @return void
*/
Reach.clearLogs(context)
```

To set device push token:
```java
/**
* @param context {@link Context}
* @param token {@link String}
* @return void
*/
Reach.setPushToken(context, token)
```

To force sensors update:
```java
/**
* @param context {@link Context}
* @return void
*/
Reach.forceSensorUpdate(context)
```

To perform actions based on Push Notifications:
```java
/**
* @param remoteMessage {@link com.google.firebase.messaging.RemoteMessage}
* @param context {@link Context}
* @return void
*/
Reach.processCommand(remoteMessage, context)
```

To listen push notifications from SixGill, register broadcast listners to intent with filter of `Reach.PUSH_BROADCAST`

```java
const pushReciever = new BroadcastReciver(){
    @Override
    public void onReceive(Context context, Intent intent) {
        String actionType = intent.getStringExtras(Reach.PUSH_TYPE)
    }
}
LocalBroadcastManager manager = LocalBroadcastManager.getInstance(context);
manager.registerReceiver(pushReciever, new IntentFilter(Reach.PUSH_BROADCAST));
```
> Note: to prevent memory leaks, always make sure to unregister recievers when not in use or context is destroyed.
See [unregistering recievers](https://developer.android.com/reference/android/content/Context.html#unregisterReceiver(android.content.BroadcastReceiver))


### Other Notes

If including `.aar` file manually, you need to put sdk dependencies in you app dependecies.
**Note: this only applies if you are including `.aar` manually instead of using sdk hosted on cloud**

[Generating .aar](https://github.com/sixgill/android-sdk-java/tree/API-integration#user-content-installation)

build.gradle(app level)
```gradle
dependencies {
    ...
    implementation 'javax.annotation:javax.annotation-api:1.2'
    implementation 'com.journeyapps:zxing-android-embedded:3.5.0'
    implementation 'com.google.code.gson:gson:2.8.0'
    implementation 'com.google.firebase:firebase-messaging:11.8.0'

    implementation 'com.squareup.retrofit2:retrofit:2.3.0'
    implementation 'com.squareup.retrofit2:converter-protobuf:2.3.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:3.9.1'
    implementation 'com.google.android.gms:play-services-gcm:11.8.0'
    implementation 'com.google.android.gms:play-services-location:11.8.0'
    implementation 'com.facebook.stetho:stetho-okhttp3:1.5.0'
    ...
}
```

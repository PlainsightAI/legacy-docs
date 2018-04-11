
Sense 2.0 : Getting Started with Sense 2.0
==========================================

On-boarding users and devices in the Sense 2.0 platform

*   [Create a Sixgill Sense 2.0 account](#creating-an-account)
*   [Creating a Channel](#creating-a-channel)
*   [Configuring a Channel for Push](#configuring-a-channel-for-push)
    *   [Apple Push](#apple-push)
    *   [Android Push](#android-push)
*   [Connecting Devices](#connecting-devices)
    *   [iOS](#ios)
    *   [Android](#android)
    *   [IoT Devices](#iot-devices)
*   [Creating a Rule](#creating-a-rule)
*   [Using the Sense API](#using-the-sense-api)

## Creating an Account
----------------------------------

You will need an account to get started with Sixgill Sense. From here, may start your own organization, or you may be invited to join an existing organization via email.

1.  Go to [sense-dashboard.sixgill.com](http://sense-dashboard.sixgill.com) and sign up for an account. You will be required to validate your email address.
    
2.  Once you are signed in, you can add other users to your organization through the Admin tab.

### Joining an Organization

You may receive an email invitation titled "Welcome to Sixgill Sense" inviting you to join an existing organization.

1. In the email, click "Accept Invite" and log into your account.

2. Click on your organization name at the top right corner and select the new organization from the drop-down.


## Creating a Channel
A data Channel is a source of data that flows into the Sense platform. For example, a Channel could include all iOS devices with an app that uses the Sense Reach iOS SDK. Channels are collections of mobile apps that use the Sense SDKs, or of any other devices that write to the Sense Ingress API.

![](images/508329985.png?height=250)

1.  In the dashboard, go to Channels. If this is your organization's first channel, you will be presented with an Intro screen. Click **Add New Channel**
2.  Click on the type of channel you would like to create
    -  Select **iOS Mobile Devices** to create a channel for iOS data for apps using the Sense Reach iOS SDK
    -  Select **Android Mobile Devices** to create a channel for Android data for apps using the Sense Reach Android SDK
    -  Select **IoT Devices** to create a channel for generic IoT data that will be sent using the Sense Ingress API
3.  Enter a name for your channel. Channel names must be unique to your account.

This will create a Channel for the type of data you wish to ingest.

## Configuring a Channel for Push
In order to receive Push notifications, the Channel will need to be configured with mobile keys depending on the app you are using to stream data. For iOS, you will need to upload the Push certificate for your app as a **.p12 file**. For Android, you will need the Firebase key or Google Cloud Messaging key used by the app.

1. Go to Channels
2. Select the mobile channel you wish to add mobile keys for.
3. Click **Manage Mobile Keys** at the top right corner  

### Apple Push ###
- If this is an iOS channel, select the type of Environment this certificate is configured for - **Development** or **Production**. This needs to match the certificate type for Push to work so double-check that you have selected the correct option.
- Click "Choose File" and select the .p12 file from your system.
- Click **Save**  
The certificate type should be listed on the left.

### Android Push ###
- If this is an Android channel, select the messaging type you will be using - **Google Cloud Messaging** or **Firebase Cloud Messaging**. This needs to match the messaging type used by the app, so double-check that you have selected the correct option.
- Enter the API key
- Click **Save**  
The selected messaging type should be listed on the left.


_Note: If you would like to configure your Sync 2.0 demo app to use Push, please contact support@sixgill.com_  

## Connecting Devices
Device sensor data is ingested into Sense 2.0 through the [Ingress API](http://docs.sixgill.com/ingress-api.html). For Android and iOS devices, this can be done by integrating the Sense SDK into a mobile app. The SDK handles sensor gathering at configurable intervals, and automatically sends the data to the Ingress API. The SDKs are authenticated using the API keys for their channel.

### iOS

Sense Reach iOS SDK v1.0.0 _(released TBD)_

[Sixgill Reach iOS SDK User Guide](http://docs.sixgill.com/ios-sdk-objc-docs/user-guide.html)

### Android

Sense Reach Android SDK v1.0.0 _(released TBD)_

[Sixgill Reach Android SDK User Guide](http://docs.sixgill.com/android-user-guide.html)
  
### Sixgill Sync 2.0
Users can also demo the mobile SDK capabilities by using the Sixgill Sync 2.0 demo application, available on the App Store and (coming soon) the Google Play Store.

Sixgill Sync 2.0 for iOS (link)

Sixgill Sync 2.0 for Android (link)

1.  Download the app on your device using the link above
2.  Login with your Sense 2.0 account credentials. iOS devices should use an **iOS Mobile Devices** Channel and Android devices should use an **Android Mobile Device** Channel

![](images/508297263.png)

_Note: The ability to scan QR codes has been temporarily removed from the Sync app._  

If using Sense 2.0 account credentials, you will be asked to select your project and channel.

![](images/508297271.png)![](images/508330046.png)

3. Your device will now connect to your selected Channel. The app will begin collection sensor data events for ingestion by the Sense platform.

You will know that Sync is connected by checking your Log Information pane. The Data section should be populated with your device data.
  

### IoT Devices

The platform is able to ingest generic IoT sensor data. Developers will need to use the [Ingress API](http://docs.sixgill.com/ingress-api.html) to send data to the IoT events endpoint.

1.  Create an **IoT Devices** Channel if you do not have one already.
2.  **Data Mapping** may be required depending on the format of your sensor data. Contact [support@sixgill.com](mailto:support@sixgill.com) with any questions regarding setting up your channel for IoT data.
3.  Use the Channel API keys to register your IoT device. Use the JSON Web Token in the response to authenticate your future requests.
4.  You can begin sending sensor data to the IoT events endpoint. See the IoT Events section of the [Ingress API](http://docs.sixgill.com/ingress-api.html) docs for more information.
  

## Creating a Rule
Rules are at the heart of device interactivity within the Sense platform. Rules define conditions around devices and sensor data and specify the actions to trigger when the conditions are met. For example, devices entering a geofence or coming within range of a beacon could trigger a notification to another device, send an email to a recipient, or post data to another web service. Rules can be complex and perform multiple actions or include data from multiple channels.

1. In the dashboard, go to Rules
2. Click **Add New Rule**
3. Enter the condition(s) for the rule. You can choose from a *Landmark Condition* or *Attribute Condition*

### Add a Landmark Condition  

Landmark - A landmark is a geofence around a certain location. It can be a circle, polygon, or rectable.

**When will the event happen?**   
First select WHEN the event will trigger in relation to the landmark.  
a) Inside Area - Trigger when device is inside this landmark
b) Exit Area - Trigger when the device was previously inside this landmark, and then leaves it   
c) Enter Area - Trigger when the device was previously outside this landmark, and then enters it    
d) Outside Area - Trigger when the device is outside this landmark  

**Which location do you want the event to trigger from?**  
Next, select the landmark for this condition
- To use an existing landmark from the project, click **Select From Project Landmarks**. This will display a list of available landmarks, if any. Clicking on the name will display the landmark in the map. You can use the **Search By Landmark Name** search box to filter the list.
- To create a new landmark, click **Add New Landmark**

![](images/rule_add_new_landmark.png)  

a) Move the map or use Google Addresses to center the map as needed  
b) Use the Drawing Tools to create a geofence around your desired area  
c) Click Use Geofence  
d) Enter a name for the landmark  
e) Click Create Landmark to save  

For testing purposes, we recommend adding a landmark around your current geographical area.  

Landmarks for the project can also be created separately in the **Landmarks** section. You can then select these landmarks when creating a rule under **Select From Project Landmarks**.

### Add an Attribute Condition
Attribute - a custom condition or freeform condition which allows you to specify attributes/properties and the logical condition(s) to be evaluated. These conditions can be simple using pre-defined attributes (Manufacturer, Device Type) or written to be more complex using the Advanced editor. The attributes being compared in the conditions will need to exist in the data stream for the device.

4. When adding conditions, be sure to select whether these are **AND or OR** conditions.  
**AND** is selected by default, meaning the device will need to satisfy all conditions to trigger the rule. For example, a device needs to be inside a landmark AND also be an iOS device to trigger the rule.  
**OR** can be used if the device only needs to satisfy one condition to trigger the rule. For example, a device needs to either be inside a landmark OR be an iOS device to trigger the rule.
 
### Add an Action
An action is the behavior that is executed when the conditions are met. An action can be in the form of sending an SMS, a Push message, an email, a webhook, or a combination of actions.  
1. Click *New Action* under the Actions section
2. Select your desired action by clicking on the action: SMS, Email, Send Push, Webhook  
#### SMS   
- **Message** - Enter the SMS message to be sent to recipients   
- **Phone Numbers** - Enter the recipient phone numbers, numerals only. Hit "Enter" after each number.
#### Email  
- **Subject** - Enter the subject line of the email   
- **Message** - Enter the desired message body  
- **Recipients** - Enter the recipient email addresses. Hit "Enter" after each address.
#### Send Push  
- **Subject** - Enter the subject line of the message  
- **Message** - Enter the desired message body  
- **Recipients** - Check the **Triggering Device** box (most commonly used). You could also specify a list of device IDs. Hit "Enter" after each device ID.
#### Webhook  
- **URL** - Enter the URL to send the webhook action to.  
- **Method** - Select the HTTP Request method to use for the action.  
- **Body** - (optional) Enter a request payload for the action.  
- **HTTP Username, HTTP Password, Header, Value** - (optional) Fill in the remaining fields as needed

Add additional actions if needed.

5. Click "Continue to next Step"
6. **Name** - Enter a name for this rule
7. **Description** - (optional) Enter a description for the rule
8. **Minimum Time Between Triggering (in Seconds)** - (optional) The minimum amount of time that must elapse before the rule can be triggered again
9. **Tags** - (optional) Enter tags for this rule
10. **Schedule** - (optional) Use this section to set a schedule for the rule. This will set limits on when the rule is enabled. By default, the rule will be enabled within 5 minutes of activation and will run indefinitely  a) **Timezone** - Select the timezone for this rule's schedule.  b) **Set timeframe (start / end)** - Check this box to set the start/end date for the rule.  c) **Only run on specific Day(s) of the week** - Check this box to set a weekly schedule for the rule.  d) **Only run on specific Hours of the Day** - Check this box to set a daily schedule for the rule. For example, you may only want the rule to run between 9:00am and 12:00pm.
11. Click "Continue to next Step". You will be prompted on whether you want to simply want to "Save" the rule but not activate it, or "Save & Activate" to enable the rule right away.

_Note: Rules will be enabled within 5 minutes of activation._


## Using the Sense API
The Sense dashboard functions such as logging in, creating projects, channels, rules, and landmarks can all be performed using the Sense API. See [Sense API Docs](http://docs.sixgill.com/sense-api.html) for more information.


  

  


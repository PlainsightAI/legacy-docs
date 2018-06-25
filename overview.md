Sense Overview
==========================================

## What is a channel?
A data channel is a source of data that flows into the Sense platform. For example, a channel could include all 
iOS devices with an app that uses the Sense Reach iOS SDK. Channels are collections of mobile apps that use the 
Sense SDKs, or of any other devices that write to the Sense Ingress API.

## What is a project?
A profile organizes your various channels and rules. You can separate the channels that are affected by the rules by grouping them into projects.

## What is a device?
A device is anything that provides sensor data to the platform. These can include mobile devices, watches, headsets, or other IoT sensors such as thermostats.

## How do devices connect to a channel?
Device sensor data is ingested into the platform through the [Ingress API](http://docs.sixgill.com/ingress-api.html). 
For Android and iOS devices, this can be done by integrating the Sense SDK into a mobile app. 
The SDK handles sensor gathering at configurable intervals, and automatically sends the data to the Ingress API. 
The SDKs are authenticated using the API keys for their respective channels.

## What are Rules?
Rules are at the heart of device interactivity within the Sense platform. Rules define conditions around devices 
and sensor data and specify the actions to trigger when the conditions are met. For example, devices entering a geofence 
or coming within range of a beacon could trigger a notification to another device, send an email to a recipient, or post 
data to another web service. Rules can be complex and perform multiple actions or include data from multiple channels.

## What are Landmarks?
Landmarks or geofences are points of interest defined by a perimeter. Landmarks are used as conditions for rules in order to trigger some action.

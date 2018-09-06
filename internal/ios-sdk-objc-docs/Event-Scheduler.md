## Event Scheduler
### Project Files
```
SGEventScheduler.h, .m
SGEventToken.h, .m
SGPullDeviceConfigEvent.h, .m
SGPullEvent.h, .m
SGPullLandmarksEvent.h, .m
SGPushEvent.h, .m
SGSendDeviceInfoEvent.h, .m
SGSendLocationEvent.h, .m
```

`SGEventScheduler` is the entity responsible for triggering push and pull events. Push events send data up to the network and pull events retrieve data. Events can be triggered by timers that are set to certain cadences, or by silent push notifications sent to the device. 

### Pull Events
Pull events encapsulate tasks such as retrieving whitelisted beacon IDs relevant to the user's current location or retrieving up-to-date device configurations. Once a pull event is triggered, the following steps happen:
1. A specific instance of `SGPullEvent` is created, either `SGPullLandmarksEvent` or `SGPullDeviceConfigEvent`.
1. The instance is handed a completion handler block. Right now the primary use for this block is to allow the SDK to communicate the result of a push notification induced background fetch back to iOS. For more information about why this is necessary see the [iOS documentation](https://developer.apple.com/reference/uikit/uiapplicationdelegate/1623013-application?language=objc).
1. The pull event's `pullData` method is called to perform the network operation

### Push Events
Like pull events, push events can be triggered by either a timer or a silent push notification. Push events are different, however, in that they are required to gather sensor data before the networking step can take place. During the process of executing a push event, an `SGEventToken` object is created to act as that event's signature as sensor data is collected, sent to the state manager and packaged into a networking packet.

The process of executing a push event goes as follows:
1. Create the correct instance of `SGPushEvent` and set it's sensors' delegates to `SGStateManager`
1. Create an `SGEventToken` and give it the following information:
    - Current date-time
    - An instance of `SGPushEvent`
    - A background fetch handler block
1. Ask State Manager to create an event completion table with the token
1. Ask all sensor managers associated with the event to perform their respective tasks and respond back to State Manager with the given event token.
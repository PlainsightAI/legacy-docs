## State Manager
### Project Files
```
SGStateManager.h, .m
```

`SGStateManager` exists to synchronize the callbacks from all the sensor managers for a given event. The intuition is this as follows: Each event (i.e. location update based triggered by cadence) involves getting readings from a set of onboard sensors. Some of the sensors return readings synchronously, others async. But, since all of the readings need to be packed into one message (see the SGMS in the protobuf file) before it can be sent through the system, State Manager is in charge of waiting until the last reading is received before the SGMS message is created and sent.

The main points of the `SGStateManager` are as follows:

```objc
@interface SGStateManager : NSObject<TaskResultDelegate>

-(void) buildCompletionTableFromEventToken:(SGEventToken *)eventToken;

-(void) taskSuccessfullyCompletedWithManagerSignature:(id<TaskManager>)manager
                                                 data:(NSDictionary *)data
                                        andEventToken:(SGEventToken *)eventToken;

@end
```

When an event is triggered, a completion table is created via `buildCompletionTableFromEventToken`. The event completion table maps event tokens to sensor group completion tables. A sensor group completion table can be thought of as a checklist that ticks off each sensor manager as it returns with a reading. The structure of the event completion table and the sensor group completion is illustrated below:

<table><tr><th colspan="3">SGEventCompletionTable</th></tr><tr><td><br></td><td colspan="2"> SGSensorGroupCompletionTable</td></tr><tr><td>SGEventToken</td><td>SGSensorManager</td><td>Completed (BOOL)</td></tr><tr><td></td><td>SGSensorManager</td><td>Completed (BOOL)</td></tr></table>

Once State Manager has determined that an event is complete, it does the following:
1. Send the `NSDictionary` containing all of the sensor reading data to the push event object associated with the event token.
1. Remove the event completion table associated with the event token
1. Remove the event listing from the index of mutable event states (the dictionary that catalogs sensor data as it is read)

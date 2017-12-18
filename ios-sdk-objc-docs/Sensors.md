## Sensors
### Project Files
```
SGActivityManager.h, .m
SGBluetoothManager.h, .m
SGConfigurationStoreManager.h, .m
SGDeviceManager.h, .m
SGLocationManager.h, .m
SGSensorManager.h, .m
SGWifiManager.h, .m
```
The Reach SDK reads and reports data from each of the device's sensors. 
A list of sensors and the data they provide are as follows:
- **Activity**: Is the user walking, running, biking or driving?
- **Bluetooth**: What are the known beacons that are currently within range?
- **Configuration**: What are the current poll cadences set at?
- **Device**: What is the device's battery status? Which OS and version is it running?
- **Location**: Where is the user currently at?
- **Wifi**: What is the SSID of the connected wifi network?

Each sensor is accessed via a subclass of the `SensorManager` object. The main points of the `SensorManager` are as follows:
```objc
@protocol TaskResultDelegate
-(void) taskSuccessfullyCompletedWithManagerSignature:(id<TaskManager>)manager
                                                 data:(NSDictionary *)data
                                        andEventToken:(SGEventToken *)eventToken;

-(void) taskFailedWithManagerSignature:(id<TaskManager>)manager
                         andEventToken:(SGEventToken *)eventToken;
@end

@interface SGSensorManager : NSObject <TaskManager>

@property (nonatomic, weak) id<TaskResultDelegate> taskResultDelegate;
@property (nonatomic, strong) SGEventToken *eventToken;

-(void) setSensorService:(id)sensorService;
-(void) performTaskWithEventToken:(SGEventToken *)eventToken;
-(NSString *) resourceIdentifier;

@end
```

The most important part of each instance of SensorManager is the `performTaskWithEventToken` method. The `eventToken` parameter will be addressed in the Model section of this document, but for now you can think of it as a context. `performTaskWithEventToken` does the necessary work to read data from the "sensor" it is charge of. 

Since some of the sensors return their readings asynchronously, this method does not return anything, but rather returns the data in the form of a `NSDictionary` to it's `taskResultDelegate`.

The delegate for each of the sensor managers is `SGStateManager`.

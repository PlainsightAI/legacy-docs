## Setup and Dependencies
### Project Files
```
Makefile
Podfile
Podfile.lock
SwitchGRPC.podspec
```

All of the project dependencies are handled with [CocoaPods](https://cocoapods.org). However, due to the project's dependence on a shared protobuf file, some additional setup steps are required beyond then usual `pod install` command. These additional steps have been encapsulated in a make file. The steps to setup the project are as follows:

1. Clone the repository: `git clone https://github.com/Sixgill/ios-sdk-objc.git`
1. Navigate to the `SixgillSDK` subdirectory
1. Run `make`
1. Open the `ios-sdk-workspace.xcworkspace` in Xcode.

If you are interested in the details of the build process, read on.

### The Makefile
The make file performs the following steps:
1. Remove existing `Pods` directory if it exists
1. Remove existing `Podfile.lock` file if it exists
1. Remove previously cloned `service` repository if it exists
1. Clean CocoaPods cache
1. Runs `pod install`

### The Podfile

Currently there are only two dependencies:
1. The [CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack) logging framework
1. The [Sixgill Service repository](https://github.com/Sixgill/service) that contains the protobuf definitions and GoMobile device switch (although this may not be used in the final implementation).

The service repository dependency is pulled down via the `SwitchGRPC` development podspec included in this repository.

### SwitchGRPC Podspec

The podspec is responsible fetching the service repository, building the objective-c protobuf and gRPC bindings, and linking them to the Xcode project. To accomplish these tasks, it performs the following steps:
1. Specifies relative paths for protoc command, gRPC Objective-C plugin, and the destination for generated files.
1. Fetches the service repository via `git clone`
1. Moves the service repository to the directory containing the iOS project
1. Copies the `google` dependencies directory into the service protobuf directory.
1. Runs `protoc` with all necessary parameters to generate the Obj-C bindings for the protobuf file 
1. Links generated files to the Xcode project
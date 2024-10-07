# ``iOS-Playback-Link-SDK``

The BVPlaybackLink SDK is an assist for dealing with BlendVision Backend API

## Requirements
- Xcode: 13 or later
- Swift: 5.5 or later
- iOS Version: 15.0+ (or iOS 14 with back-deployment)

## Installation
### Swift Package Manager

[Swift Package Manager](https://swift.org/package-manager/) is a tool for managing the distribution of Swift frameworks. It integrates with the Swift build system to automate the process of downloading, compiling, and linking dependencies.

#### Using Xcode
1. Open your Xcode project.
2. Select `File -> Swift Packages -> Add Package Dependency`.
3. In the pop-up window, enter the framework's Git URL: https://github.com/BlendVision/iOS-Playback-Link-SDK.git
4. Select the desired version, then click Next to add it.

#### Using `Package.swift`
To integrate using Apple's Swift Package Manager, add the following as a dependency to your `Package.swift` and replace `Version Number` with the desired version of the SDK.

```swift
.package(name: "BVPlaybackLink", url: "https://github.com/BlendVision/iOS-Playback-Link-SDK", .exact("Version Number"))
```

And then specify the `BVPlayer` as a dependency of the desired target. Here is an example of a `Package.swift` file:

```swift
let package = Package(
  ...
  dependencies: [
    ...
    .package(name: "BVPlaybackLink", url: "https://github.com/BlendVision/iOS-Playback-Link-SDK", .exact("Version Number"))
  ],
  targets: [
    .target(name: "<NAME_OF_YOUR_PACKAGE>", dependencies: ["BVPlaybackLink"])
  ]
  ...
)
```

# BVSessionManager
## Features
- **Playback Token Update**: A simple interface to update the playback token.
- **Session Management**: Start or stop a session.
- **Heartbeat Mechanism**: Send periodic heartbeat requests to keep the session active.
- **Error Handling**: Automatically capture and handle network and server errors.
- **Support for Asynchronous Operations**: Perform network requests using Swift's `async/await`.

## Usage

### Get the `BVSessionManager`

```swift
import BVPlaybackLink

let sessionManager = BVSessionManager.shared

```


### Update the playback token to the playbackLink
```swift
BVSessionManager.shared.updatePlaybackToken("your-playback-token")
```


### Observes the playbackLink error and status event
```swift
BVSessionManager.shared.add(listener: self)
```


### Get the resource info for analysis
You can get the resource info for analysis by calling the getResourceInfo() method. ResourceInfo contains the id and type of the resource.
```swift
do {
    let contentMetadata = try await BVSessionManager.shared.getResourceInfo()
    print("Resource ID: \(contentMetadata.resourceID), Resource Type: \(contentMetadata.resourceType)")
} catch {
    print("Failed to get resource info: \(error)")
 }
```
Use the resourceInfo to set the moduleConfig in the Player instance
```swift
let config: [String: String] = [
    "analytics.resource_id": info.resourceID,
    "analytics.resource_type": info.resourceType
]
player = UniPlayerFactory.create(player: playerConfig, moduleConfig: config)
```


### To start a playback session, which sends a heartbeat to increase concurrent viewers
```swift
do {
    try await BVSessionManager.shared.startPlaybackSession()
} catch {
    print("Failed to start session: \(error)")
}
```


### To end a playback session, which stops the heartbeat to decrease concurrent viewers
```swift
do {
    try? await BVSessionManager.shared.endPlaybackSession()
} catch {
    print("Failed to stop session: \(error)")
}
```


### Heartbeat Mechanism
The heartbeat mechanism will automatically start during the playback session without any additional setup.


### Error Handling
If a listener is added, all errors will automatically be sent to `BVSessionErrorEvent`.
```swift
extension YourViewController: BVSessionListener {
    func onEvent(_ event: BVSessionErrorEvent, manager: BVSessionManager) {
        print("Error occurred: \(event.error), message: \(event.message)")
    }
}
```  


### Status Monitoring
If a listener is added, all successful status updates will automatically be sent to `BVSessionStatusEvent`.
```swift
extension YourViewController: BVSessionListener {
    func sessionManager(_ manager: BVPlaybackLink.BVSessionManager, 
                                          didReceiveErrorEvent event: BVPlaybackLink.BVSessionErrorEvent) {
        print("Status occurred: \(event.status)")
    }
}
```

## Samples
For samples using the BVPlaybackLink SDK see [here](https://github.com/BlendVision/iOS-Playback-Link-Sample). For a sample using the Swift Package Manager for integration, see sample named `PlaybackLinkSample`.

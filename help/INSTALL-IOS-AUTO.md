# iOS Auto-linking Installation
### `react-native >= 0.60`

### With `yarn`

```shell
yarn add @deliverysolution/react-native-proximity-sdk
```
```
yarn add react-native-background-fetch@~4.1.0
```
```
yarn add react-native-background-geolocation@~4.10.0
```
```
yarn add @react-native-async-storage/async-storage
```

### With `npm`
```shell
npm install @deliverysolution/react-native-proximity-sdk --save
```
```
npm install react-native-background-fetch@~4.1.0
```
```
npm install react-native-background-geolocation@~4.10.0
```
```
npm install @react-native-async-storage/async-storage
```


## pod install

```bash
$ cd ios
$ pod install
```

## Configure Background Capabilities

Open your App in **XCode** and select the root of your project.  Select **Capabilities** tab.  Enable **Background Modes** and enable the following modes:

- [x] Location updates
- [x] Background fetch
- [x] Audio (**optional for debug-mode sound FX**)

## Configure Usage Strings in `Info.plist`

Edit **`Info.plist`**.  Add the following items (Set **Value** as desired):

| Key | Type | Value |
|-----|-------|-------------|
| *Privacy - Location Always and When in Use Usage Description* | `String` | *CHANGEME: Location required in background* |
| *Privacy - Location When in Use Usage Description* | `String` | *CHANGEME: Location required when app is in use* |
| *Privacy - Motion Usage Description* | `String` | *CHANGEME: Motion permission helps detect when device in in-motion* |

![](https://dl.dropboxusercontent.com/s/j7udsab7brlj4yk/Screenshot%202016-09-22%2008.33.53.png?dl=1)

## Privacy Manifest
To avoid fingerprinting and another potential risk, Apple now requires apps and third-party SDKs to surface information related to Type of Data Collected, Tracking Different Domains and Groups of API that require approved reasons. (For more information refer [link](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_data_use_in_privacy_manifests))

If the privacy manifest file does not exist, then you can create one following the information mentioned in [apple doc](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/adding_a_privacy_manifest_to_your_app_or_third-party_sdk). Once create, you will find the **PrivacyInfo.xcprivacy** file right under the **ios/** directory.

Add the following four blocks within the `NSPrivacyAccessedAPITypes` `<array>` dictionary:
```javascript
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">

<plist version="1.0">
<dict>
    <key>NSPrivacyAccessedAPITypes</key>
    <array>
        <!-- [1] background_fetch: UserDefaults -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>

            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>CA92.1</string>
            </array>
        </dict>

        <!-- [2] background_geolocation: UserDefaults -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>

            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>CA92.1</string>
                <string>1C8F.1</string>
            </array>
        </dict>
        <!-- [3] background_geolocation (CocoaLumberjack): FileTimestamp -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>C617.1</string>
                <string>0A2A.1</string>
            </array>
        </dict>
        <!-- [4] background_geolocation (CocoaLumberjack): DiskSpace -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryDiskSpace</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>E174.1</string>
            </array>
        </dict>
    </array>
</dict>
</plist>
```

## [Configure `react-native-background-fetch`](https://github.com/transistorsoft/react-native-background-fetch/blob/master/docs/INSTALL-AUTO-IOS.md#configure-background-capabilities)

The BackgroundGeolocation SDK makes use internally on __`react-native-background-fetch`__.  Regardless of whether you instend to implement the BackgroundFetch Javascript API in your app, you **must** perform the [Background Fetch iOS Setup](https://github.com/transistorsoft/react-native-background-fetch/blob/master/docs/INSTALL-AUTO-IOS.md#configure-background-capabilities) at __`react-native-background-fetch`__.

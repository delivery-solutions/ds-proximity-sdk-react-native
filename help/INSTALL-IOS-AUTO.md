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


## `pod install`

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


## [Configure `react-native-background-fetch`](https://github.com/transistorsoft/react-native-background-fetch/blob/master/docs/INSTALL-AUTO-IOS.md#configure-background-capabilities)

The BackgroundGeolocation SDK makes use internally on __`react-native-background-fetch`__.  Regardless of whether you instend to implement the BackgroundFetch Javascript API in your app, you **must** perform the [Background Fetch iOS Setup](https://github.com/transistorsoft/react-native-background-fetch/blob/master/docs/INSTALL-AUTO-IOS.md#configure-background-capabilities) at __`react-native-background-fetch`__.

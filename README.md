# ds-proximity-sdk-react-native

The Delivery Solutions Proximity SDK for React Native enables retailers to easily add customer tracking and proximity calculation Delivery Solutions' in-store pickup and curbside experience to their React Native mobile application.

## Proximity SDK Prerequisite
* A SDK Token and License Key from your Delivery Solutions CSM
* Locations created in your Delivery Solutions accounts

## Path to Live
* Grant permission to use location services
* Configure license key
* Add a access token
* Add a tracking Id 
* Add a location Id unique to your system and configured in the Delivery Solutions portal
* Implement trigger to start tracking
* Go on a test drive

## Getting Started

This guide helps you to get started with Delivery Solutions Proximity SDK for React Native. Run the following command into an active React Native application to install the SDK into you current application.

### Installing dependencies

You will need Node, Yarn, Watchman, the React Native command line interface, Android Studio and Xcode.

```
brew install yarn
brew install watchman
```

### Adding SDK

```
npm install ds-proximity-sdk-react-native
```

or with Yarn

```
yarn install ds-proximity-sdk-react-native
```

⚠️ If you have a previous version of react-native < 0.60, you should try link to link the plugin with the following command. 

```
react-native link ds-proximity-sdk-react-native
```

## Android Implementation

Navigate to <Project directory>/android/build.gradle and add the below URLs inside allProjects -> repositories block:

```
maven {
  url("${project(':ds-proximity-sdk-react-native').projectDir}/libs")
}
maven {
  url("${project(':react-native-background-fetch').projectDir}/libs")
}
```

## iOS Implementation

Navigate to <Project directory>/iOS from terminal and run the below command:

```
pod install
```

### Usage
Import the DS Proximity Tracking plugin as below:

```
import DSProximity from 'ds-proximity-sdk-react-native';
```

### Start Tracking
To start tracking, we have 2 variations:
1. With a location Id 
2. With a destination object

To specify a destination using a location Id, pass the below object consisting Delivery Solutions ```storeExternalId```

```
let launchObject = {
  authToken: '<token>', // Token for authentication; provided by Delivery Solutions
  username: '<USER-ID>', // Identifier for the user
  trackingId: '1234', // A unique identifier to track the trip
  locationId: '23' // ```storeExternalId``` as registered with Delivery Solutions
};

DSProximity.startTracking(
  launchObject,
  (isTracking) => {
    console.log('Success : ' + isTracking); // if isTracking is true, the tracking has started else the tracking has stopped
  },
  (error) => {
    console.log('Error : ' + error);
  }
);
```

To start tracking, when you have the destination object {latitude: value, longitude: value} handy while calling the start tracking function then you can pass the following object in DeliverySolution.startTracking();

```
let launchObject = {
  authToken: '<token>', // Token for authentication; provided by Delivery Solutions
  username: '<USER-ID>', // Identifier for the user
  trackingId: '1234', // A unique identifier to track the trip
  destination: { // Optional. If destination is passed, the tracking stops when user reaches these coordinates
    latitude: 30.2720039,
    longitude: 78.0892899
  },
};

DSProximity.startTracking(
  launchObject,
  (isTracking) => {
    console.log('Success : ' + isTracking); // if isTracking is true, the tracking has started else the tracking has stopped
  },
  (error) => {
    console.log('Error : ' + error);
  }
);
```

### Stop Tracking

By default the plugin stops tracking automatically when the user reaches the destination based on geo-fencing radius configured for the location. However, there might be cases when the user would want to stop the tracking manually. In those cases, call the below function to stop tracking.

```
DSProximity.stopTracking(
  (isTracking) => {
    console.log('Success : ' + isTracking); // false means tracking has stopped
  },
  (err) => {
    console.log('Error : ' + err);
  }
);
```
### Get Tracking State

In some cases you may want to check the state of tracking, i.e. whether the tracking is live or stopped. You can call the below method to get the tracking state.

```
DSProximity.getTrackingState((isTracking) => {
  console.log('tracking state: ' + isTracking); //true indicates the tracking is in progress
});
```

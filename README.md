# Official Proximity SDK for React Native

<p align="center" width="100%">
    <img src="https://raw.githubusercontent.com/delivery-solutions/ds-proximity-sdk-react-native/main/Proximity%20SDK.png">
</p>

&nbsp;

Delivery Solutions' Proximity Tracking SDK for React Native

## Links

 - [Contact Us](https://www.deliverysolutions.co/contact-us)
 - [NPM Package](https://www.npmjs.com/package/@deliverysolutions/react-native-proximity-sdk)


## Integration Flow

<p align="center" width="100%">
    <img width="33%" src="https://raw.githubusercontent.com/delivery-solutions/ds-proximity-sdk-react-native/main/sdk-functions.png">
</p>



## Installing the SDK

### With `yarn`

```bash
yarn add @deliverysolution/react-native-proximity-sdk
yarn add react-native-background-fetch
```

### With `npm`
```
npm install @deliverysolution/react-native-proximity-sdk --save
npm install react-native-background-fetch --save
```

## Setup Guides

### `react-native >= 0.60`

### iOS
- [Auto-linking Setup](https://github.com/delivery-solutions/ds-proximity-sdk-react-native/blob/main/help/INSTALL-IOS-AUTO.md)

### Android
- [Auto-linking Setup](https://github.com/delivery-solutions/ds-proximity-sdk-react-native/blob/main/help/INSTALL-ANDROID-AUTO.md)

## Configure your license (Only for Android)

Add the below entry in your android/app/src/main/res/values/strings.xml file (Create the file if it does not exists):

```xml
<resources>
    <-- <other string entries go here> -->
  <string name="proximity_sdk_license_key">YOUR_LICENSE_KEY</string>
</resources>
```
[How to get YOUR_LICENSE_KEY](#faq)

## Usage

Import and use Proximity SDK as below:

```js
import Proximity from '@deliverysolutions/react-native-proximity-sdk';

class App extends Component {

  componentDidMount() {
    Proximity.init({
      accessToken: "<YOUR-ACCESS-TOKEN>",
      distanceInterval: 100,
      testMode: true
    }).then( async () => {
      // check the SDK's current tracking state.
      const isTracking = await Proximity.getTrackingState()
      console.log(`Tracking state: ${isTracking}`);

      // setup geofence handler
      Proximity.onGeofence = (isTracking) => {
        // stuff to do here, when user enters geofence
      }

      Proximity.onArrived = (isTracking) => {
        // stuff to do here, when user manually calls arrived event
      }

      Proximity.onCompleted = () => {
        // stuff to do here, when user manually calls completed event or order is marked completed
      }

      Proximity.onUpdateVehicleDetails = (vehicleDetails) => {
        // stuff to do here, when user updates the vehicle details via updateVehicleDetails()
      }

    })
  }

  handleStartTrackingClick = async () => {

    // Set vehicle details before you start tracking, this is optional.
    await Proximity.updateVehicleDetails({
      vehicleColor: 'silver | black | white | grey | blue | red | brown | green | teal | yellow',
      vehicleMake: 'string',
      vehicleType: 'hatchback | micro | minivan | pickup | sedan | truck | suv',
      vehicleNumber: 'string',
      description: 'string',
      parkingSlot: 'string',
      isPickupBySomeoneElse: 'boolean',
      userName: 'string',
      contactNumber: 'string'
    })

    // Initiates tracking using the Order Id. Alternatively you can also use toggle tracking handler that toggles tracking based on the current tracking state.
    const isTracking = await Proximity.startTracking({
      orderId: '<ORDER-ID>'
    })
    console.log(`Tracking state: ${isTracking}`);
  }

  handleStopTrackingClick = async () => {
    // Terminates tracking manually.
    const isTracking = await Proximity.stopTracking()
    console.log(`Tracking state: ${isTracking}`);
  }
}
```

## Methods

### Init tracking

To initialize tracking, call the init method, this will authorise the SDK with the provided `accessToken` against your tenant and prepare it for tracking. accessToken must be environment dependant - for testMode as true our sandbox accessToken and for testMode as false use the production token.

[How to get accessToken](#faq)

`testMode` (default - false) is used to switch between sandbox and production environments.

```js
let initObj = {
  accessToken: '<YOUR-ACCESS-TOKEN>', // associated with your tenant
  testMode: true // (Optional) Default is false
  distanceInterval: 50, // (Optional) Default is 50 (meters)
  geofenceRadius: 200,  // (Optional) Default is 200 (meters)
};

Proximity.init(initObj);
```

:warning: Warning: You must call init once and only once, each time your app is launched.
Do not hide the call to #ready within a view which is loaded only by clicking a UI action. This is particularly important for iOS in the case where the OS relaunches your app in the background when the device is detected to be moving. If you don't ensure that init is called in this case, tracking will not resume.

### Start tracking

This will start the tracking process for the provided orderId. The SDK will prompt the user to provide access to Location Services in case the app has no permissions set for it. This prompt is shown when the `startTracking` method is triggered. 

Please note, that if Location Permission is denied, then the SDK will be unable to send location to the Delivery Solutions System and only manual ETA updates would work.

In case the permission is set to `When In Use`, the the location updates will be sent only when the app is in use, the user has to set the Location Permission to `Always` such that the Delivery Solutions System receives location updates even when the phone is in background.

```js
const isTracking = await Proximity.startTracking({
  orderId: '<ORDER-ID>' // provide orderId of the order that you want to track
});
```

### Updating ETA (Manually) 

Manual ETA Updating should be used for following - 
- User has not provided access to location and wants to manually 
- User even during the automatic flow, wants to manually notify the ETA (changes flow to manual)

An ISO 8601 timestamp is provided as the parameter indicating the new ETA.

```js
await Proximity.updateETA('<ORDER-ID>', '2021-10-06T11:13:10.038Z');
```

### Arriving to the store

As in the order lifecycle, store can be informed of arrival by using markArrived function.

markArrived could be called on click of a button such as "I've Arrived"

Please note, tracking will continue even though the person has arrived.

```js
await Proximity.markArrived('<ORDER-ID>'); 
```

### Completing an Order

As in the order lifecycle, store can be informed of the completion of order by using markCompleted function.

Generally, this is to be called when the person has collected the order and wants to notify the store that to the store and stop location tracking. This should be called only once.



```js
await Proximity.markCompleted('<ORDER-ID>'); 
```


### Stop tracking

By default the SDK stops tracking only when the order is marked as completed by calling markAsCompleted function. However, there might be cases when the user would want to stop the tracking manually. Call the below function to stop tracking:

```js
const isTracking = await Proximity.stopTracking(); // false will be returned when tracking is stopped successfully.
```

### Toggle tracking

This method is a wrapper over `stopTracking` and `startTracking` methods, it will initialise tracking in case the SDK is not tracking otherwise it will stop tracking, it returns the current tracking state of the SDK.

```js
const isTracking = await  Proximity.toggleTracking({
  orderId: '<ORDER-ID>' // provide orderId of the order that you want to track
});
```

### Get tracking state

In some cases you would want to check the state of tracking i.e. whether the tracking is already going on or not. You can call the below method to get the tracking state:

```js
const isTracking = await  Proximity.getTrackingState();  // true indicates the tracking is in progress
```

### Set vehicle details

The following method is used to accept the vehicle details and the contact information of the user whose location is being tracked.

```js
const trackingMetaData = await  Proximity.updateVehicleDetails(
  '<ORDER-ID>',
  {
  vehicleColor: 'silver | black | white | grey | blue | red | brown | green | teal | yellow',
  vehicleMake: 'string',
  vehicleType: 'hatchback | micro | minivan | pickup | sedan | truck | suv',
  vehicleNumber: 'string',
  description: 'string',
  parkingSlot: 'string',
  isPickupBySomeoneElse: 'boolean',
  userName: 'string',
  contactNumber: 'string'
});
```

## Events

### On Geofence

This event is triggered when the user whose location is being tracked enters the vicinity of the adress of the associated order. If there is anything that you want to do once the user reaches it's destination, then that can be done here.

```js
Proximity.onGeofence = (isTracking) => {
  // stuff to do here, when user enters geofence
};
```
### On Arrived

This event is triggered when the markArrived function is called.

```js
Proximity.onArrived = (isTracking) => {
  // stuff to do here, when the location tracking has started
};
```
### On Update Vehicle Details

This event is triggered when the order's vehicle details are updated by the user.

```js
Proximity.onUpdateVehicleDetails = ( updatedVehicleDetails ) => {
  // stuff to do here, when the order's vehicle details are updated
};
```
### On Completed

This event is triggered when the order has been marked as completed either by the user or by the store operator.

```js
Proximity.onCompleted = () => {
  // stuff to do here, when the order has been completed
};
```
### On Arrived

This event is triggered when the markArrived function is called.

```js
Proximity.onArrived = (isTracking) => {
  // stuff to do here, when the location tracking has started
};
```
### On Update Vehicle Details

This event is triggered when the order's vehicle details are updated by the user.

```js
Proximity.onUpdateVehicleDetails = () => {
  // stuff to do here, when the order's vehicle details are updated
};
```
### On Completed

This event is triggered when the order has been marked as completed either by the user or by store operator.

```js
Proximity.onCompleted = () => {
  // stuff to do here, when the order has been completed
};
```

## FAQ

#### How can I get the licence key / accessToken?

Please raise a ticket with [DS support](https://www.deliverysolutions.co/contact-us) with details about the usage and the team shall provide the required keys.

#### I have an Android/iOS native codebase instead of React Native.

We also offer native Android and iOS SDKs. Please contact our support team for more details.


## Event-Status Mapping

| event | DS status     | Description                |
| :-------- | :------- | :------------------------- |
| `started` | `PICKUP_STARTED` | The user started their journey  |
| `geofence` | `PICKUP_STARTED` | The user is within the geofence  |
| `arrived` | `PICKUP_AT_LOCATION` | The user has arrived to the store  |
| `completed` | `ORDER_DELIVERED` | Order was delivered / picked up.  |

## License

Copyright (c) 2022 by [Delivery Solutions](https://www.deliverysolutions.co). All Rights Reserved. Usage of this library implies agreement to abide by the license terms. Please refer to our [Terms of Service](https://www.deliverysolutions.co/terms-of-service).

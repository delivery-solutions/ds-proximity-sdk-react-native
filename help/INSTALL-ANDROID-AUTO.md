# Android Auto-linking Installation

### `react-native >= 0.60`

### With `yarn`

```shell
yarn add @deliverysolution/react-native-proximity-sdk
```
```
yarn add react-native-background-fetch@~4.2.1
```
```
yarn add react-native-background-geolocation@~4.13.1
```
```
yarn add @react-native-async-storage/async-storage
```


### With `npm`
```shell
npm install @deliverysolution/react-native-proximity-sdk --save
```
```
npm install react-native-background-fetch@~4.2.1
```
```
npm install react-native-background-geolocation@~4.13.1
```
```
npm install @react-native-async-storage/async-storage
```

## Configure `react-native-background-fetch`

You must perform the [Android Setup](https://github.com/transistorsoft/react-native-background-fetch/blob/master/docs/INSTALL-AUTO-ANDROID.md) for `react-native-background-fetch`.

## Gradle Configuration

Add the following `ext` variables to control the version of Google dependency versions the plugin will align to.

:information_source: You should always strive to use the latest available Google Play Services libraries.  You can determine the latest available version [here](https://developers.google.com/android/guides/setup).

In addition, custom `maven url` for both `background-geolocation` and `background-fetch` are required.

:information_source: Note: Some recent versions of the React Native Android template may not include the allprojects section. You should add this manually as a separate section along with the nested repositories section in the same `android/build.gradle file`.

### :open_file_folder: **`android/build.gradle`**

```diff
buildscript {
    ext {
        minSdkVersion = 16
        targetSdkVersion = 31           // Or higher.
+       compileSdkVersion = 31          // Or higher.
+       appCompatVersion = "1.4.2"      // Or higher.  Required for new AndroidX compatibility.
+       googlePlayServicesLocationVersion = "20.0.0"  // Or higher.
    }
    ...
}

allprojects {
    repositories {
        mavenLocal()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url("$rootDir/../node_modules/react-native/android")
        }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }
+       maven {
+           // Required for react-native-background-geolocation
+           url("${project(':react-native-background-geolocation').projectDir}/libs")
+       }
+       maven {
+           // Required for react-native-background-fetch
+           url("${project(':react-native-background-fetch').projectDir}/libs")
+       }
+    }
}
```

### :open_file_folder: **`android/app/build.gradle`**

Background Geolocation requires a gradle extension for your `app/build.gradle`.

```diff
project.ext.react = [
    entryFile: "index.js",
    enableHermes: false,  // clean and rebuild if changing
]

apply from: "../../node_modules/react-native/react.gradle"

+Project background_geolocation = project(':react-native-background-geolocation')
+apply from: "${background_geolocation.projectDir}/app.gradle"
```


## Android 10 and *When in Use* Location Authorization

Android 10 introduces *When in Use* location authorization.  If you're building with `compileSdkVersion 29`, add the following elements to your **`AndroidManifest.xml`**.  This allows your app to continue location-tracking when location-services are initiated while your app is in the foreground.


```diff
<manifest>
    <application>
+       <service android:name="com.transistorsoft.locationmanager.service.TrackingService" android:foregroundServiceType="location" />
+       <service android:name="com.transistorsoft.locationmanager.service.LocationRequestService" android:foregroundServiceType="location" />
    </application>
</manifest>

```


## Proguard Config


If you've enabled **`def enableProguardInReleaseBuilds = true`** in your `app/build.gradle`, be sure to add the BackgroundGeolocation SDK's `proguard-rules.pro` to your **`proguardFiles`**:

### :open_file_folder: `android/app/build.gradle`)

```diff
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
.
.
.
android {
    .
    .
    .
    buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            // Add following proguardFiles (leave existing one above untouched)
+           proguardFiles "${background_geolocation.projectDir}/proguard-rules.pro"
            signingConfig signingConfigs.release
        }
    }
}
```

:warning: If you get error `"ERROR: Could not get unknown property 'background_geolocation' for project ':app'"`, see [above](#open_file_folder-androidappbuildgradle) and make sure to define the `Project background_geolocation`.




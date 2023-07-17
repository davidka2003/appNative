This is a new [**React Native**](https://reactnative.dev) project, bootstrapped using [`@react-native-community/cli`](https://github.com/react-native-community/cli).

# Getting Started

>**Note**: Make sure you have completed the [React Native - Environment Setup](https://reactnative.dev/docs/environment-setup) instructions till "Creating a new application" step, before proceeding.

## Step 1: Start the Metro Server

First, you will need to start **Metro**, the JavaScript _bundler_ that ships _with_ React Native.

To start Metro, run the following command from the _root_ of your React Native project:

```bash
# using npm
npm start

# OR using Yarn
yarn start
```

## Step 2: Start your Application

Let Metro Bundler run in its _own_ terminal. Open a _new_ terminal from the _root_ of your React Native project. Run the following command to start your _Android_ or _iOS_ app:

### For Android

```bash
# using npm
npm run android

# OR using Yarn
yarn android
```

### For iOS

```bash
# using npm
npm run ios

# OR using Yarn
yarn ios
```

If everything is set up _correctly_, you should see your new app running in your _Android Emulator_ or _iOS Simulator_ shortly provided you have set up your emulator/simulator correctly.

This is one way to run your app — you can also run it directly from within Android Studio and Xcode respectively.

## Step 3: Modifying your App

Now that you have successfully run the app, let's modify it.

1. Open `App.tsx` in your text editor of choice and edit some lines.
2. For **Android**: Press the <kbd>R</kbd> key twice or select **"Reload"** from the **Developer Menu** (<kbd>Ctrl</kbd> + <kbd>M</kbd> (on Window and Linux) or <kbd>Cmd ⌘</kbd> + <kbd>M</kbd> (on macOS)) to see your changes!

   For **iOS**: Hit <kbd>Cmd ⌘</kbd> + <kbd>R</kbd> in your iOS Simulator to reload the app and see your changes!

## Congratulations! :tada:

You've successfully run and modified your React Native App. :partying_face:

### Now what?

- If you want to add this new React Native code to an existing application, check out the [Integration guide](https://reactnative.dev/docs/integration-with-existing-apps).
- If you're curious to learn more about React Native, check out the [Introduction to React Native](https://reactnative.dev/docs/getting-started).

# Troubleshooting

If you can't get this to work, see the [Troubleshooting](https://reactnative.dev/docs/troubleshooting) page.

# Learn More

To learn more about React Native, take a look at the following resources:

- [React Native Website](https://reactnative.dev) - learn more about React Native.
- [Getting Started](https://reactnative.dev/docs/environment-setup) - an **overview** of React Native and how setup your environment.
- [Learn the Basics](https://reactnative.dev/docs/getting-started) - a **guided tour** of the React Native **basics**.
- [Blog](https://reactnative.dev/blog) - read the latest official React Native **Blog** posts.
- [`@facebook/react-native`](https://github.com/facebook/react-native) - the Open Source; GitHub **repository** for React Native.

# CodePush integration

First you need to install these packages:

```bash
#if u don't have appcenter-cli installed:
npm install -g appcenter-cli

#package
yarn add react-native-code-push

#additional packages
yarn add appcenter appcenter-analytics appcenter-crashes --save-exact
```

Login with appcenter:

```bash
appcenter login
```

Then run this command from root of the project to install ios deps:
```bash
cd ios && pod install && cd ..
```

Need to change file AppDelegate.mm:
```c
// Add these imports to the top of AppDelegate.mm or AppDelegate.m file
// AppCenter CodePush
#import <AppCenterReactNative.h>
#import <AppCenterReactNativeAnalytics.h>
#import <AppCenterReactNativeCrashes.h>
#import <CodePush/CodePush.h>
#import "AppDelegate.h"
```

```c
#if DEBUG
  return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index"];
#else
   //Change this line to return [CodePush bundleURL];
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
```

Add to info.plist DeploymentKey obtained from appcenter dashboard (Distribute => CodePush):
```xml
<key>CodePushDeploymentKey</key>
<string>Your-deployment-key</string>
```

To deploy app by air (will work only if you haven't changed native dependencies):

```bash
appcenter codepush release-react -a acc/app-name -d Production
```

# Background location setup

## iOS
### 1) Linking (just use pod install)
For RN 0.60 or higher, no manual linking is needed. After installing the package, just run `pod install` from inside ios directory. It'll automatically pickup the package and install it.

<details>
<summary>0.59 or below</summary>

### Using Cocoapods
- Update your `Podfile`
    ```
    pod 'react-native-geolocation-service', path: '../node_modules/react-native-geolocation-service'
    ```
 - Then run `pod install` from ios directory

### Manually linking

#### `Open project.xcodeproj in Xcode`

Drag `RNFusedLocation.xcodeproj` to your project on Xcode (usually under the Libraries group on Xcode):

![xcode-add](../screenshots/01-ios-add-to-library.png?raw=true)

#### Link `libRNFusedLocation.a` binary with libraries

Click on your main project file (the one that represents the `.xcodeproj`) select `Build Phases` and drag the static library from the `Products` folder inside the Library you are importing to `Link Binary With Libraries` (or use the `+` sign and choose library from the list):

![xcode-link](../screenshots/02-ios-add-to-build-phases.png?raw=true)
</details>

### 2) Enable Swift Support
Since the iOS implementation is written in swift, you need to add swift support in your project. It can be done just by adding an empty swift file and a bridging header in your project folder. You have to do it from xcode, otherwise swift compiler flag won't be updated.
- Select `File -> New -> File` from xcode
- Choose Swift file, name it anything
- Click `Next` and say yes when prompted if you’d like to generate a bridging header (important)

### 3) Update `info.plist`
There are three info.plist keys for location service
- NSLocationWhenInUseUsageDescription
- NSLocationAlwaysUsageDescription
- NSLocationAlwaysAndWhenInUseUsageDescription

Unless you need background location update, adding only the first key will be enough. To enable background location update, you need to add all the keys in `info.plist` and add location as a background mode in the `Signing & Capabilities -> Capability` tab in Xcode.

## Android

### 1) Linking
For RN 0.60 or higher, no manual linking is needed. You can override following gradle properties from your root build.gradle file.

```gradle
ext {
  compileSdkVersion   = 28
  buildToolsVersion   = "28.0.3"
  minSdkVersion       = 16
  targetSdkVersion    = 28

  // Any of the following will work
  googlePlayServicesVersion      = "17.0.0"
  // playServicesVersion         = "17.0.0"
  // playServicesLocationVersion = "17.0.0"
}
```

<details>
<summary>0.59 or below</summary>

1. In `android/app/build.gradle`

    ```gradle
    ...
    dependencies {
        ...
        implementation project(':react-native-geolocation-service')
    }
    ```

    If you've defined [project-wide properties](https://developer.android.com/studio/build/gradle-tips#configure-project-wide-properties) (recommended) in your root build.gradle, this library will detect the presence of the following properties:

    ```gradle
    buildscript {
        /**
         + Project-wide Gradle configuration properties
         */
        ext {
            compileSdkVersion   = 28
            minSdkVersion       = 16
            targetSdkVersion    = 28
            buildToolsVersion   = "28.0.3"
            googlePlayServicesVersion = "17.0.0"
        }
        repositories { ... }
        dependencies { ... }
    }
    ```

    If you do not have *project-wide properties* defined and have a different play-services version than the one included in this library, use the following instead. But play service version should be `11+` or the library won't work.

    ```gradle
    ...
    dependencies {
        ...
        implementation(project(':react-native-geolocation-service')) {
            exclude group: 'com.google.android.gms', module: 'play-services-location'
        }
        implementation 'com.google.android.gms:play-services-location:<insert your play service version here>'
    }
    ```

2. In `android/setting.gradle`

    ```gradle
    ...
    include ':react-native-geolocation-service'
    project(':react-native-geolocation-service').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-geolocation-service/android')
    ```

3. In `MainApplication.java`

    ```java
    ...
    import com.agontuk.RNFusedLocation.RNFusedLocationPackage;

    public class MainApplication extends Application implements ReactApplication {
        ...
        @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                ...
                new RNFusedLocationPackage()
            );
        }
    }
    ```
</details>

### 2) Permissions
Add permission in your `AndroidManifest.xml` file based on your project requirement.
```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
Setup
=====

Expecting you have the React Native development environment in place, are
starting with a React Native hello world project and have managed to run it on
an actual Android/iOS device:

* Install library via npm

    ```
    npm install --save react-native-audio-toolkit
    ```

* Follow the platform specific steps for each platform you wish to support. Note: `react-native link` will not work correctly for android and you'll still have to manually do step 1 below in order to avoid "Configuration with name 'default' not found."

### Android setup

1. Append dependency to end of `android/settings.gradle` file

    ```
    ...

    include ':react-native-audio-toolkit'
    project(':react-native-audio-toolkit').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-audio-toolkit/android/lib')
    ```

2. Add dependency to `android/app/build.gradle`

    ```
    ...

    dependencies {
        ...
        compile project(':react-native-audio-toolkit')
    }
    ```

3. Register the module in `android/app/src/main/java/com/<project>/MainApplication.java`

    ```java
    package com.<project>;

    import com.facebook.react.ReactActivity;
    import com.facebook.react.ReactPackage;
    import com.facebook.react.shell.MainReactPackage;

    import com.futurice.rctaudiotoolkit.AudioPackage; // <-------- here

    ...

    protected List<ReactPackage> getPackages() {
        return Arrays.<ReactPackage>asList(
            ...
            new MainReactPackage(),
            new AudioPackage() // <------------------------------- here
        );
    }
    ```

4. (optional) Doing specific tasks with this library requires adding permissions to your
    Android manifest file, which can be found at `android/app/src/main/AndroidManifest.xml`

    ```xml
    <manifest ...>

        <!-- If you want to play audio from a SD card (i.e. external storage),
             you need to add this permission -->
        <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

        <!-- If you want to play audio from a URL, you need to add these permissions -->
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

        <!-- If you want to record audio, you need to add this permission -->
        <uses-permission android:name="android.permission.RECORD_AUDIO" />

        <!-- If you want to record audio to a SD card (i.e. external storage),
             you need to add this permission -->
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        ...

    </manifest>
    ```

    For versions of Android earlier than Android 6.0, the user is asked to agree to permission
    when installing the app. However, on Android 6.0+ the app developer is responsible for
    asking for permissions before they are required.
    
    For an example of this in action, check out the code in the ExampleApp at
    `ExampleApp/src/App.js` or check out the documentation for
    [PermissionsAndroid](https://facebook.github.io/react-native/docs/permissionsandroid).

### iOS setup

1. Right click `Libraries`, click `Add Files to "ExampleApp"`

2. Select `node_modules/react-native-audio-toolkit/ios/ReactNativeAudioToolkit/ReactNativeAudioToolkit.xcodeproj`

3. Select your app from the Project Navigator, click on the `Build Phases` tab.
    Expand `Link Binary With Libraries`. Click the plus and add
    `libReactNativeAudioToolkit.a` from under Workspace.
    
4. Add a usage description to **Info.plist**.
    ```<key>Privacy - Microphone Usage Description</key>
       <string>This app requires access to your microphone</string>
    ```

### Play some media!

* Include the JavaScript library:

    ```js
    import {
        Player,
        Recorder,
        MediaStates
    } from 'react-native-audio-toolkit';

    ...
    ```

* Create a button for triggering our example:

    ```js
    import {
      ...
      TouchableHighlight
    } from 'react-native';

    class MyApp extends React.Component {
      constructor() {
        super();
        this.state = {
          disabled: false
        };
      }

      _onPress() {
        ...
      }

      render() {
        return (
          ...
          <TouchableHighlight disabled={this.state.disabled} onPress={() => this._onPress()}>
            <Text>
              Press me!
            </Text>
          </TouchableHighlight>
          ...
    ```

* Fill in `_onPress()` handler with example code:

    ```js
    _onPress() {
      // Disable button while recording and playing back
      this.setState({disabled: true});

      // Start recording
      let rec = new Recorder("filename.mp4").record();

      // Stop recording after approximately 3 seconds
      setTimeout(() => {
        rec.stop((err) => {
          // NOTE: In a real situation, handle possible errors here

          // Play the file after recording has stopped
          new Player("filename.mp4")
          .play()
          .on('ended', () => {
            // Enable button again after playback finishes
            this.setState({disabled: false});
          });
        });
      }, 3000);
    }
    ```

* Run app with `react-native run-android` or `react-native run-ios`

### Where to go next?

- Check out the [API documentation](/docs/API.md) for more information.
- Examples on playback from various [media sources](/docs/SOURCES.md)

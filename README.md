Android emulator: 
 - Pixel 2 API 28 Android 9.0 Google APIs | arm64-v8a
Step reproduce:

- Init project: npx react-native init Test0714

- android/gradle.properties ->  newArchEnabled true

- add @react-native-firebase/app

- register firebase console -> add google-services.json (packageName: "com.test0714") to dir: /android/app

- rnfirebase docs: => cd android && ./gradlew signingReport

- add "classpath 'com.google.gms:google-services:4.3.15'" into dir: /android/build.gradle

- Lastly, execute the plugin by adding the following to your /android/app/build.gradle file:
      ->apply plugin: 'com.google.gms.google-services' // <- Add this line

- In File: /android/app/build.gradle 
    => add these lines: "multiDexEnabled true" and "implementation 'androidx.multidex:multidex:2.0.1'"

- yarn add @react-native-firebase/messaging

- yarn add @react-native-firebase/crashlytics
  - android/build.gradle add line: "classpath 'com.google.firebase:firebase-crashlytics-gradle:2.9.4'"
  - android/app/build.gradle add line:  "apply plugin: 'com.google.firebase.crashlytics'"

// run-android here working fine.

\/ \/ \/ \/ \/ \/ \/ \/
// Crash after do these things with react-native-code-push
// adb logcat ==> java.lang.UnsatisfiedLinkError: couldn't find DSO to load: libhermes.so

- yarn add react-native-code-push
  - android/setting.gradle:
      include ':app', ':react-native-code-push'
      project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
  - android/app/build.gradle
      apply from: "../../node_modules/react-native/react.gradle"
      apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
  - update MainApplication.java
      // 1. Import the plugin class.
      import com.microsoft.codepush.react.CodePush;

      //
      @Override
      protected String getJSBundleFile() {
          return CodePush.getJSBundleFile();
      }

- Run build: npx react-native run-android
*Android emulator: 
 - Pixel 2 API 28 Android 9.0 Google APIs | arm64-v8a

*System:
    OS: macOS 13.0.1
    CPU: (8) arm64 Apple M1
    Memory: 64.81 MB / 16.00 GB
    Shell: 5.8.1 - /bin/zsh
  Binaries:
    Node: 16.18.0 - /opt/homebrew/opt/node@16/bin/node
    Yarn: 1.22.19 - /opt/homebrew/bin/yarn
    npm: 8.19.2 - /opt/homebrew/opt/node@16/bin/npm
    Watchman: 2022.10.31.00 - /opt/homebrew/bin/watchman
  Managers:
    CocoaPods: 1.11.3 - /opt/homebrew/bin/pod
  SDKs:
    iOS SDK:
      Platforms: DriverKit 22.2, iOS 16.2, macOS 13.1, tvOS 16.1, watchOS 9.1
    Android SDK: Not Found
  IDEs:
    Android Studio: 2021.3 AI-213.7172.25.2113.9014738
    Xcode: 14.2/14C18 - /usr/bin/xcodebuild
  Languages:
    Java: 11.0.16.1 - /Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home/bin/javac
  npmPackages:
    @react-native-community/cli: Not Found
    react: 18.2.0 => 18.2.0 
    react-native: 0.71.4 => 0.71.4 
    react-native-macos: Not Found
  npmGlobalPackages:
    *react-native*: Not Found

// ======================================================================

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
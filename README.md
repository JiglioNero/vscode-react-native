# React Native Tools

[![Build status](https://dev.azure.com/vscode-webdiag-extensions/VS%20Code%20WebDiag%20extensions/_apis/build/status/vscode-react-native%20%5Bmaster%5D)](https://dev.azure.com/vscode-webdiag-extensions/VS%20Code%20WebDiag%20extensions/_build/latest?definitionId=60)

This VS Code extension provides a development environment for React Native projects.
Using this extension, you can **debug your code and quickly run `react-native` commands** from the command palette.

![React Native features](images/react-features.gif)


<!-- TABLE OF CONTENTS -->

# Table of Contents

- [Getting started](#getting-started)
- [React Native commands in the Command Palette](#react-native-commands-in-the-command-palette)
- [Debugging React Native applications](#debugging-react-native-applications)
  - [Hermes (Android)](#hermes-android)
  - [iOS devices](#ios-devices)
  - [Custom scheme for iOS apps](#custom-scheme-for-ios-apps)
  - [Expo applications](#expo-applications)
    - [Configuring Expo](#configuring-expo)
  - [Windows applications](#react-native-for-windows)
  - [TypeScript and Haul](#typescript-and-haul)
  - [Debugger configuration properties](#debugger-configuration-properties)
- [Customization](#customization)
  - [Logging](#logging)
  - [Build APK and generate bundle](#build-apk-and-generate-bundle)
  - [Specifying custom arguments for `react-native run-*` command](#specifying-custom-arguments-for-react-native-run--command)
  - [Setting up the react-native packager](#setting-up-the-react-native-packager)
  - [Change project root](#change-project-root)
- [Developing inside a Docker Container](#developing-inside-a-docker-container)
- [Contributing](#contributing)
- [Known Issues](#known-issues)

# Getting started

Before going any further make sure that you:

* [have a working React Native environment](https://reactnative.dev/docs/environment-setup).
* are using [VS Code](https://code.visualstudio.com) and have [installed this extension from the Marketplace](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native).
* have your React Native project root folder open in VS Code.

Please notice that the extension uses `.vscode/.react` directory at the project root to store intermediate files required for debugging. Although these files usually get removed after debug session ends, you may want to add this directory to your project's `.gitignore` file.

# React Native commands in the Command Palette

In the Command Palette, type `React Native` and choose a command.

![React Native commands](images/command-palette.png)

The **Run Android** command triggers `react-native run-android` and starts your app for Android.

The **Run iOS** command similarly triggers `react-native run-ios` and starts your app in the iOS simulator (e.g. iPhone 6).

The **Packager** commands allow you to start/stop the [**Metro Bundler**](https://github.com/facebook/metro-bundler) (formerly React Packager).

The full list of commands is:

|Name|Description|
|---|---|
|RunAndroidSimulator|Run an Android application on Emulator|
|RunAndroidDevice|Run an Android application on Device|
|RunIosSimulator|Run an iOS application on Simulator|
|RunIosDevice|Run an iOS application on Simulator|
|RunExponent|Run Expo application|
|StartPackager|Start Packager|
|StopPackager|Stop Packager|
|RestartPackager|Restart Packager|
|PublishToExpHost|Publish to Expo|
|ShowDevMenu|Show Dev Menu|
|ReloadApp|Reload an application|
|RunInspector|Run Element Inspector|

# Debugging React Native applications

To start debugging create a new debug configuration for your ReactNative app in your `.vscode/launch.json`. Adding a new configuration can be done by opening your `launch.json` file and clicking on `Add Configuration...` button and choosing a relevant debug configuration. All available debug configurations for ReactNative can be accessed by typing in *ReactNative* and picking one from the list populated by Intellisense as shown in the image below.

![Add React Native debug configuration](images/add-debug-configuration.gif)

In case if you haven't created the `.vscode/launch.json` file yet, you can add a whole default debug configuration set. To do that click the debug icon ![Choose React Native debugger](images/debug-view-icon.png) in the View bar, and then click the configuration (gear) icon ![Configure-gear](images/configure-gear-icon.png), then choose the React Native debug environment.

![Choose React Native debugger](images/choose-debugger.png)

VS Code will generate a `launch.json` in your project with some default configuration settings as shown below. You can safely close this file, choose the appropriate configuration in the Configuration dropdown, and then press F5 (or click _Green Arrow_ ![Configure-gear](images/debug-icon.png) button) to start debugging your app in VS Code.

![React Native launch targets](images/debug-targets.png)

Once app is loaded and running, [open the developer menu](https://reactnative.dev/docs/debugging#accessing-the-in-app-developer-menu) inside your application and enable remote debugging by clicking on `Debug JS Remotely` button.

![React Native enable remote debug](images/enable-remote-debug.png)

The extension allows you to debug multiple devices and configurations, please read the following sections for more information for your particular use case.

## Hermes (Android)

Hermes is the new JavaScript engine optimized for running React Native apps on Android. It improves app performance and decreases app size.

Click [here](https://reactnative.dev/docs/hermes) to learn more about Hermes.

Debugging apps with Hermes enabled is currently experimental. Please, see [this issue](https://github.com/microsoft/vscode-react-native/issues/1073) for current known issues on Hermes support.

To debug while using Hermes engine, please choose one of the following debug configurations:
 - React Native (Hermes): Debug Android - Experimental

```json
{
    "name": "Debug Android (Hermes) - Experimental",
    "cwd": "${workspaceFolder}",
    "type": "reactnativedirect",
    "request": "launch",
    "platform": "android"
}
```

 - React Native (Hermes): Attach to Hermes application - Experimental

```json
{
    "name": "Attach to Hermes application - Experimental",
    "cwd": "${workspaceFolder}",
    "type": "reactnativedirect",
    "request": "attach"
}
```

## iOS devices

Debugging on an iOS device requires following manual steps:
* Install [ios-deploy](https://www.npmjs.com/package/ios-deploy) `npm install -g ios-deploy`.
* Install a valid iOS development certificate.
* In your project's `launch.json` file set `target` to `device`. If you need to specify the exact device to run, you can set `target` to `device=<iOS_device_name>`, or you can also use `runArguments` property to specify a particular device to run on in case multiple devices are connected (e.g. `"runArguments": [ "--device", "My iPhone" ]`)
* Choose the **Debug iOS** option from the "Configuration" dropdown and press F5.
* Shake the device to open the development menu and select "Debug JS Remotely".

## Customs scheme for iOS apps

If you want to use a custom scheme for your application you can either pass it as part of the `runArguments` parameter arguments, or set the `scheme` configuration parameter as shown below:
```js
"runArguments": ["--scheme", "customScheme", ...]
// or
"runArguments": ["--scheme=customScheme", ...]
// or
"scheme" : "customScheme"
```
Please be aware, specifying the scheme value as a part of the `runArguments` parameter arguments will override the `scheme` configuration parameter value, if it set.

## Expo applications

To debug a project created using Expo or the `create-react-native-app` task, you can use embedded support for Expo.

Your environment must meet the following prerequisites:

- Install the [Expo app](https://getexponent.com/) on the target device or emulator
- Ensure that the `react-native-cli` is installed globally (`npm install -g react-native-cli`)

To start debugging in Expo follow these steps:

1. Open your project in VS Code with this extension installed.
1. Create a debug configuration (as described in [Debugging React Native applications](#debugging-react-native-applications)), select `Debug in Exponent` in the debug drop-down menu, and start debugging
1. Wait while some dependencies are configured - the extension will install `xde` and `@expo/ngrok` when this feature is used for the first time.
1. If you have not used Exponent on this system before, you will be prompted for an Exponent username and password.
   Exponent account allows you to use Expo cloud services. More info about how it works is available [here](https://docs.expo.io/versions/latest/workflow/how-expo-works/).
   If you have not created an Exponent account, then specifying a new username and password will create one.
   Note that there is no e-mail associated with the account, and no way to recover a forgotten password.
   If you don't want to create an Exponent account, you can specify `expoHostType` parameter in your debug configuration to make Expo work locally (via LAN or on localhost).
1. Once the packager starts, the extension will open a separate tab with QR code to scan from the Exponent app. Once you do so, the Exponent app will connect to the packager and begin running your app.
1. Once the app is loaded and running, [open the developer menu](https://reactnative.dev/docs/debugging#accessing-the-in-app-developer-menu) and enable remote debugging by clicking on `Debug JS Remotely` button.

   ![React Native developer menu](./images/enable-remote-debug.png)

   From here you can run and debug the app as normal.

### Configuring Expo

The extension supports running through Exponent not just the applications created with Expo but even pure React Native applications (in that case you need to add `expo` package to `node_modules` in order to make it work with Expo: `npm install expo --save-dev`. In either cases it uses `app.json` configuration file in the root of the project.

If you are running `Debug in Exponent` configuration or any of pallette commands like `Run in Exponent`, `Publish to Exponent` then this file will be created automatically if absent or updated with the following basic configuration section:

```json
{
  "expo": {
    "slug": "MyApp", // Project slug
    "name": "MyApp", // Project name
    "sdkVersion": "31.0.0", // Expo SDK version
    "entryPoint": ".vscode\\exponentIndex.js" // Entrypoint for the project
  },
  "name": "MyApp" // Project name
}
```

Full list of configuration parameters for `expo` section in `app.json` may be found on [official Expo documentation page](https://docs.expo.io/versions/latest/workflow/configuration).

For running **pure React Native app**, the extension, creates and uses `.vscode/exponentIndex.js` which points to the app entrypoint (`index.js` or `index.android.js` or `index.ios.js`) file.

If you want to change your app entrypoint (for example, from `index.js` to `index.android.js`), delete `.vscode/exponentIndex.js` and then restart your debugging session.

**NOTE**: The extension caches the version of the exponent SDK used by your project. This is helpful since we don't want to install the SDK each time you run exponent. If you want the extension to update the SDK version based on your React Native version, just restart VS Code and if it is supported it should work. If it does not please open an issue.

## Windows applications

You can debug UWP and WPF React Native for Windows applications by changing the `platform` in your `launch.json` configuration:

* For `UWP` use `windows`:

  ```json
  {
      "name": "Debug UWP",
      "cwd": "${workspaceFolder}",
      "type": "reactnative",
      "request": "launch",
      "platform": "windows"
  }
  ```

* For `WPF` use `wpf`:

  ```json
  {
      "name": "Debug WPF",
      "cwd": "${workspaceFolder}",
      "type": "reactnative",
      "request": "launch",
      "platform": "wpf"
  }
  ```

## TypeScript and Haul

If you use [Haul](https://callstack.github.io/haul/users.html) as your React Native bundler instead of the default [Metro](https://facebook.github.io/metro/), you must add `sourceMapPathOverrides` to the `launch.json` file.

For example:

```json
{
  // Other configurations
  "sourceMapPathOverrides": {
      "webpack:///./~/*":   "${workspaceRoot}/node_modules/*",
      "webpack:///./*":   "${workspaceRoot}/*",
      "webpack:///*":     "*"
  }
}
```

Learn more about source map overrides [here](https://github.com/Microsoft/vscode-node-debug2#sourcemappathoverrides)


## Debugger configuration properties

The following is a list of all the configuration properties the debugger accepts in `launch.json`:

|Name |Description|Type|Defaults|
|---|---|---|---|
|`cwd`|The path to the project root folder|`string`|`${workspaceFolder}`|
|`sourceMaps`|Whether to use JavaScript source maps to map the generated bundled code back to its original sources|`boolean`|`true`|
|`sourceMapPathOverrides`|A set of mappings for rewriting the locations of source files from what the source map says, to their locations on disk. See [Debugging with TypeScript and Haul](#typescript-and-haul) for details|`object`|n/a|
|`trace`|Logging level in debugger process. May be useful for diagnostics. If set to "Trace" all debugger process logs will be available in `Debug Console` output window|`string`|`log`|
|`address`|TCP/IP address of packager to attach to for debugging|`string`|`localhost`|
|`port`|Port of packager to attach to for debugging|`string`|`8081`|
|`remoteRoot`|The source root of the remote host|`string`|`null`|
|`localRoot`|The local source root that corresponds to the 'remoteRoot'|`string`|`${workspaceFolder}`|
|`skipFiles`|An array of file or folder names, or glob patterns, to skip when debugging|`array`|`[]`|
|`debuggerWorkerUrlPath`|Path to the app debugger worker to override. For example, if debugger tries to attach to http://localhost:8081/debugger-ui/debuggerWorker.js and you get 404 error from packager output then you may want to change debuggerWorkerUrlPath to another value suitable for your packager (\"debugger-ui\" will be replaced with the value you provide)|`string`|`debugger-ui/`|
|`platform`|The platform to target. Possible values: `android`, `ios`, `exponent`, `windows`, `wpf`|`string`|n/a|
|`target`|Target to run on. Possible values: `simulator`, `device`, `device=<iOS device name>`, [`<Android emulator/device id>`](https://github.com/react-native-community/cli/blob/master/docs/commands.md#--deviceid-string), `<iOS simulator name>`|`string`|`simulator`|
|`logCatArguments`|Arguments to be used for LogCat (The LogCat output will appear on an Output Channel). It can be an array such as: `[":S", "ReactNative:V", "ReactNativeJS:V"]`|`array`|`["*:S", "ReactNative:V", "ReactNativeJS:V"]`|
|`runArguments`|Run arguments to be passed to `react-native run-<platform>` command (override all other configuration params)|`array`|n/a|
|`launchActivity`|The Android activity to be launched for debugging, e.g. it specifies [`--main-activity`](https://github.com/react-native-community/cli/blob/master/docs/commands.md#--main-activity-string) parameter in `react-native` run arguments|`string`|`MainActivity`|
|`expoHostType`|The connection type to be used on Expo debugging to communicate with a device or an emulator. Possible values: <ul><li>`tunnel` - allows to deploy and debug an application by means of Expo cloud services</li><li>`lan` - allows to deploy and install an application via your LAN</li><li>`local` - allows to debug an application on an emulator or an Android device without network connection</li></ul>|`string`|`tunnel`|
|`env`|Environment variables passed to the debugger and `react-native run-<platform>` command|`object`|`{}`|
|`envFile`|Absolute path to a file containing environment variable definitions|`string`|`${workspaceFolder}/.env`|
|`variant`|A variant to be passed to `react-native run-android`, e.g. use `devDebug` to specify `--variant=devDebug`|`string`|n/a|
|`scheme`|A scheme name to be passed to `react-native run-ios`, e.g. `devDebug` to specify `--scheme=devDebug`|`string`|n/a|
|`productName`|iOS bundle display name e.g. `AwesomeProject` value means that the extension will search for `AwesomeProject.app` bundle|`string`|n/a|

# Customization

The extension can be further customized for other React Native scenarios. These are the most common:

## Logging

To expose internal logs to the output, set the following properties:

```json
{
  "react-native-tools": {
    "logLevel": "Trace"
  }
}
```

`logLevel` can be `None` (no logs), `Error`, `Warning`, `Info`, `Debug`, `Trace` (all logs). Default is `Info`.


## Build APK and generate bundle

You can add VSCode tasks to build an `.apk` file and generate iOS/Android bundles.

The following is an example of a `tasks.json` for `react-native init` projects.
Place it in the `.vscode` folder in your project to use it:

```json
{
    "version": "2.0.0",
    "presentation": {
        "reveal": "always",
        "panel": "new"
    },
    "tasks": [
        {
            "taskName": "Build APK Debug",
            "group": "build",
            "type": "shell",
            "windows": {
                "command": "cd android; if($?) {./gradlew assembleDebug}"
            },
            "linux": {
                "command": "cd android && ./gradlew assembleDebug"
            }
        },
        {
            "taskName": "Build APK Release",
            "group": "build",
            "type": "shell",
            "windows": {
                "command": "cd android; if($?) {./gradlew assembleRelease}"
            },
            "linux": {
                "command": "cd android && ./gradlew assembleRelease"
            }
        },
        {
            "taskName": "Generate Android Bundle",
            "group": "build",
            "type": "shell",
            "command": "react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/main.jsbundle"
        },
        {
            "taskName": "Generate iOS Bundle",
            "group": "build",
            "type": "shell",
            "command": "react-native bundle --platform ios --dev false --entry-file index.js --bundle-output ios/main.jsbundle"
        }
    ]
}
```

To learn more about `tasks` in VSCode read [the official documentation](https://code.visualstudio.com/docs/editor/tasks).

Visit [generating Signed APK](https://reactnative.dev/docs/signed-apk-android.html) to learn more about this subject.

## Specifying custom arguments for `react-native run-*` command

Using custom run arguments for `react-native run-<platform>`:
**NOTE:** This overrides all other configuration parameters.

```json
{
  "react-native.android.runArguments.simulator": ["--appFolder", "/Users/test/AwesomeProject/android/app", "--deviceId", "emulator-5555"],
  "react-native.ios.runArguments.device": ["--project-path", "ios", "--device", "Max's iPhone"],
}
```

**NOTE:** You can get the list of installed simulator devices by:

iOS devices (macOS only):

```
xcrun simctl list --json devices
```

Android devices:

```
adb devices
```

**NOTE:** If you want to run the application on an iOS device, make sure you have `ios-deploy` installed globally.

```npm install -g ios-deploy```

## Setting up the react-native packager

To use a custom port for the `react-native` packager:

```json
{
  "react-native": {
    "packager" : {
      "port": portNumber
    }
  }
}
```

If you change this port, then for iOS device and simulator scenarios you will have to modify the native code files. [Instructions here](https://blog.binoy.io/running-react-native-on-a-different-port-7deb43887cd4).

If you use Android, you need to change the debug server by:

1. CTRL+M(CMD+M) in the emulator
2. Go to Dev Settings
3. Debug server host for device => enter ‘localhost:\<yourPortNumber\>’.
4. Reload application (press `R` twice)

**NOTE:** Some aspects of React Native hard-code the port to the default as specified in [this issue](https://github.com/facebook/react-native/issues/9145).


## Change project root

To specify a subfolder in which the react-native project is located, set `react-native-tools.projectRoot`. You can use either an absolute or relative path here:

```json
{
  "react-native-tools": {
    "projectRoot": "./your/react-native/project"
  }
}
```

# Developing inside a Docker Container

The extension supports [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview) features on Linux. Please follow the [VS Code official documentation](https://code.visualstudio.com/docs/remote/containers) to setup your environment to use a remote development approach.

You can use [official React Native Docker image](https://hub.docker.com/r/reactnativecommunity/react-native-android) provided by the [react-native-community](https://github.com/react-native-community/docker-android).

Here are the steps to run React Native debugging inside a Docker Container on a real Android device:

1. Open Command Palette and run the following command
    ```
    Remote-Containers: Add Development Container Configuration Files...
    ```
    Then select `Existing Dockerfile` to create `.devcontainer/devcontainer.json` configuration file.
1. Сreate Dockerfile extending [reactnativecommunity/react-native-android image](https://hub.docker.com/r/reactnativecommunity/react-native-android). For example you can use the following Dockerfile:
    ```
    FROM reactnativecommunity/react-native-android:latest

    RUN npm install -g expo-cli react-native-cli
    ```

1. Configure your `devcontainer.json` file as needed. Below is a sample configuration:
    ```json
    {
        "name": "React Native Android Container",

        // Sets the run context to one level up instead of the .devcontainer folder.
        "context": "..",

        // Update the 'dockerFile' property if you aren't using the standard 'Dockerfile' filename.
        "dockerFile": "Dockerfile",

        // The optional 'runArgs' property can be used to specify additional runtime arguments.
        "runArgs": [
            "--privileged", // give all capabilities to a container, in other words, the container can then do almost everything that the host can do
            "--net", "host", // forwarding all host machine ports
            "-v", "/dev/bus/usb:/dev/bus/usb" // mount connected USB devices to a container
        ],

        "settings": {
            // This will ignore your local shell user setting for Linux since shells like zsh are typically
            // not in base container images. You can also update this to an specific shell to ensure VS Code
            // uses the right one for terminals and tasks. For example, /bin/bash (or /bin/ash for Alpine).
            "terminal.integrated.shell.linux": null
        },

        // Add the IDs of extensions you want installed when the container is created in the array below.
        "extensions": ["msjsdiag.vscode-react-native"]
    }
    ```

1. Open Command Palette and run the following command `Remote-Containers: Open Folder in Container` to reopen your project in a container
1. Connect your device via USB and start debugging the same way as on local machine.

Currently the above scenario doesn't work on macOS and Windows. Docker Container implementation on these OS uses Virtual Machine tools which may have problems with USB forwarding for mobile devices.

# Contributing

Please see our [contributing guide](CONTRIBUTING.md) for more information.

# Known Issues

Here is the list of common known issues you may experience while using the extension:

Issue                                | Description
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------
Debugger doesn't stop at breakpoints | Breakpoints require sourcemaps to be correctly configured. If you are using TypeScript, then make sure to follow the `Getting started` section for how to ensure sourcemaps are correctly set up. Also, similar issues may occur on React Native version `0.58.*` in some special cases (see [#928](https://github.com/Microsoft/vscode-react-native/issues/928), [#907](https://github.com/Microsoft/vscode-react-native/issues/907)), bumping dependencies versions of `react` and `react-native`  package to the more recent ones should resolve these.
'adb: command not found'             | If you receive an error `adb: command not found`, you need to update your system Path to include the location of your *ADB* executable.The *ADB* executable file is located in a subdirectory along with your other Android SDK files.
Targeting iPhone 6 doesn't work      | There was a known issue with React Native ([#5850](https://github.com/facebook/react-native/issues/5850)) but it was fixed. Please upgrade your version of React Native.
Can't communicate with socket pipe    | (Linux only) If you have two workspaces open that only differ in casing, the extension will fail to communicate effectively.
"Add configuration" button doesn't work when trying to add debug configuration to `launch.json` | You may need to have to add in some json to `launch.json` manually. Please, see ([#985](https://github.com/Microsoft/vscode-react-native/issues/985))

[Known-Issues](https://github.com/Microsoft/vscode-react-native/issues?q=is%3Aissue+label%3Aknown-issues) provides a complete list of active and resolved issues.

# Telemetry reporting

VS Code React Native extension collects usage data and sends it to Microsoft to help improve our products and services. Read our [privacy statement](https://www.visualstudio.com/en-us/dn948229) to learn more.

If you don’t wish to send usage data to Microsoft, edit `VSCodeTelemetrySettings.json` file at `~/.vscode-react-native` and add `optIn:false`.

# Code of conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

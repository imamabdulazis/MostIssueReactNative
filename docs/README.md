# Most Issue React Native

## What is the difference between React Native and React?

[ReactJS](https://reactjs.org/) is a JavaScript library, supporting both front-end web and being run on a server, for building user interfaces and web applications. It follows the concept of reusable components.

[React Native](https://reactnative.dev/) is a mobile framework that makes use of the JavaScript engine available on the host, allowing you to build mobile applications for different platforms (iOS, Android, and Windows Mobile) in JavaScript that allows you to use ReactJS to build reusable components and communicate with native components [further explanation](https://stackoverflow.com/questions/41124338/does-react-native-compile-javascript-into-java-for-android)

Both follow the JSX syntax extension of JavaScript. Which compiles to React.createElement calls under the hood. [JSX in-depth](https://reactjs.org/docs/jsx-in-depth.html)

Both are open-sourced by Facebook

## Hide keyboard in react-native

The problem with keyboard not dismissing gets more severe if you have ***keyboardType='numeric'***, as there is no way to dismiss it.

Replacing View with ScrollView is not a correct solution, as if you have multiple textInputs or buttons, tapping on them while the keyboard is up will only dismiss the keyboard.

Correct way is to encapsulate View with ***TouchableWithoutFeedback*** and calling ***Keyboard.dismiss()***

EDIT: You can now use ***ScrollView*** with ***keyboardShouldPersistTaps='handled'*** to only dismiss the keyboard when the tap is not handled by the children (ie. tapping on other textInputs or buttons)

If you have

```js
<View style={{flex: 1}}>
    <TextInput keyboardType='numeric'/>
</View>

```
Change it to


```js
<ScrollView contentContainerStyle={{flexGrow: 1}}
  keyboardShouldPersistTaps='handled'
>
  <TextInput keyboardType='numeric'/>
</ScrollView>

```
or

```js
import {Keyboard} from 'react-native'

<TouchableWithoutFeedback onPress={Keyboard.dismiss} accessible={false}>
    <View style={{flex: 1}}>
        <TextInput keyboardType='numeric'/>
    </View>
</TouchableWithoutFeedback>

```

EDIT: You can also create a Higher Order Component to dismiss the keyboard.

```js
import React from 'react';
import { TouchableWithoutFeedback, Keyboard, View } from 'react-native';

const DismissKeyboardHOC = (Comp) => {
  return ({ children, ...props }) => (
    <TouchableWithoutFeedback onPress={Keyboard.dismiss} accessible={false}>
      <Comp {...props}>
        {children}
      </Comp>
    </TouchableWithoutFeedback>
  );
};
```
const DismissKeyboardView = DismissKeyboardHOC(View)
Simply use it like this

```js
render() {
    <DismissKeyboardView>
        <TextInput keyboardType='numeric'/>
    </DismissKeyboardView>
}
```

NOTE: the accessible={false} is required to make the input form continue to be accessible through VoiceOver. Visually impaired people will thank you!

## App Registry Error in React Native

<p>
As you create your application and run it, you may face an issue stating, “Your App is not registered.” The reason for this lies in your core process while creating application i.e., the application name in your app.json file. Just correcting the name itself will resolve this issue.
</p>

##  Development Server Error in React Native

<p>
If you see a screen like as shown in image for Development Server Error, saying “could not connect to the development server,” then it’s an issue with your metro bundler, and you need to restart it to resolve the issue. For this, just close your previous metro bundler => Navigate to “Your Project Directory” => hit command => “npm start” and you are good to go. Again, in another tab, just run “react-native run-android/react-native run-ios” for running your application.
</p>


## Library Error (Packages not installed properly) in React Native

<p>
While using React Native Firebase library, let’s say for Push Notifications, you will get some issues like “FirebaseCore.h not found” or “Firebase.h not found.” For issues like this, its an indication that your library is not properly installed or linked. To resolve these kinds of problems, you just need to re-install and re-link your library manually again instead of linking it automatically.
</p>

## Undefined is not an object error in React Native

<p>
Many times, you will see an error saying “Undefined is not an object,” which just means that some of your references are not adequately defined or declared correctly. The variable it is referring to can be found out in the issue description as shown in image.
</p>

## The module is not found an error in React Native

<p>
Many times, you will create your .js files in a hurry, and while using them, you forget to give correct path and Boom, the issue arises. So don’t get confused, provide the right file path for files, and you will be good to go.
</p>


## App crash on launch

If your React Native app crashes on launch, and the buddybuild SDK has not been installed, it may not be obvious where to start looking to diagnose the problem.

The most common cause of this issue is a missing main.jsbundle file. Local builds appear to work because the file is available. When the file is missing from your repository, buddybuild can successfully build your React Native app but the app crashes on launch.

To confirm the exact cause of a crash on launch, examination of the device’s logs is necessary. If the buddybuild SDK has been installed, you can check the dashboard for a crash report. Otherwise, see getting device logs from Xcode.

## Troubleshooting Errors
### Unable to resolve module <module name> from <path>: Module does not exist in the module map or in these directories:

<p>
We’ve found that this error is generally caused by using wildcards (^ or ~) in your package.json.

Our recommendation is to either remove the wildcards, or to move to using yarn and committing your yarn.lock.
</p>


### Could not get BatchedBridge, make sure your bundle is packaged properly

<p>
This indicates that your app is not correctly configured to generate offline bundles, which is required for apps generated using continuous integration systems like buddybuild.

Look to Make sure your app is configured to generate an offline bundle as to how to configure your app to resolve this.
</p>


## fatal error: ***React/RCTViewManager.h*** file not found

<p>
This generally indicates that you are using a buddybuild-generated scheme that is not correctly adding <strong>libReact.a</strong> as a target dependency.

In these instances, we recommend that you share your scheme and configure your app to use your shared scheme rather than buddybuild’s generated scheme. See Missing schemes for details.
</p>


## error: FBSDKShareKit/FBSDKShareKit.h file not found

This is generally a result of an incorrectly configured repository with regards to the location of FBSDK dependencies.

If you take a look at: 
```url
https://github.com/facebook/react-native-fbsdk/blob/master/ios/RCTFBSDK.xcodeproj/project.pbxproj
```

You will notice that RCTFBSDK will look for dependencies in one of two locations:

- ***~/Documents/FacebookSDK***
- ***$(PROJECT_DIR)/../../../ios/Frameworks***

The second option is the correct option for continuous integration systems like buddybuild.

In other words, you MUST place your FBSDK dependencies under the ***ios/Frameworks*** folder in order for it to work on a continuous integration system.


## /node_modules/react-native-root-siblings/lib/AppRegistryInjection.js: Module does not exist in the module map


<p>
This error indicate that there is an incompatibility between one of your dependencies and the version of React Native set in your <strong>package.json</strong> file. For this specific error, <strong>react-native version 0.48</strong> (or higher) is required.
</p>


## error: method does not override or implement a method from a supertype

<p>If you have upgraded to React Native 0.48 (or higher) and start seeing these errors, it means that one of your dependencies set in your <strong>package.json</strong> file is incompatible with that version of React Native and needs to be updated.

</p>


## In other issue depend on the library

```fix
Happy coding
Cheers Imun.
```
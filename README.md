# Cordova Push Notifications Plugin for Android, iOS.

## DESCRIPTION

Modified pushplugin(https://github.com/phonegap-build/PushPlugin) to minimise the data sent from the server to the device.keysare only sent which are to be mapped to the strings saved locally in the application.

Modified Push-Plugin the way to display the messages in the notification bar.
Meassages sent from the server contains the key to display the messages.
And keys are mapped to strings which u can save in localised files for each platform separately.
Android:(%projdir%/res/values/string.xml) and IOS:(%ProjDir%/Resources/en.lproj).
And this plugin doesn't display any popup on the screen.

This plugin is for use with [Cordova](http://incubator.apache.org/cordova/), and allows your application to receive push notifications on Android and iOS devices.
* The Android implementation uses [Google's GCM (Google Cloud Messaging) service](http://developer.android.com/guide/google/gcm/index.html).
* The iOS version is based on [Apple APNS Notifications](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

**Important** - Push notifications are intended for real devices. The registration process will fail on the iOS simulator. Notifications can be made to work on the Android Emulator, however doing so requires installation of some helper libraries, as outlined [here,](http://www.androidhive.info/2012/10/android-push-notifications-using-google-cloud-messaging-gcm-php-and-mysql/) under the section titled "Installing helper libraries and setting up the Emulator".


**IOS** 

1) Add below plugins if they were not added before
	a)org.apache.cordova.dialogs
	b)org.apache.cordova.device
	e)org.apache.cordova.media
	f)org.apache.cordova.console
2) For localization feature please update the content in XX.lproj/Localizable.strings

### Manual Installation for iOS

Copy the following files to your project's Plugins folder:

```
AppDelegate+notification.h
AppDelegate+notification.m
PushPlugin.h
PushPlugin.m
```

Add a reference for this plugin to the plugins section in `config.xml`:

```xml
<feature name="PushPlugin">
  <param name="ios-package" value="PushPlugin" />
</feature>
```

Add the `PushNotification.js` script to your assets/www folder (or javascripts folder, wherever you want really) and reference it in your main index.html file.

```html
<script type="text/javascript" charset="utf-8" src="PushNotification.js"></script>
```

**Android**

1. Add plugin "PushPlugin" downloaded via github link   to the cordova project.
	eg: cordova plugin add "%dir%\PushPlugin-master"
2. Copy the localization files from %dir%\Example\android\values into your project android platform folder %dir%\platforms\android\res
3. Copy the Images files from %dir%\Example\android\drawable into your project android platform folder %dir%\platforms\android\res
3. Integrate the www folder, the code uses a few libraries that you can ignore make sure that you include <script type="text/javascript" charset="utf-8" src="plugins/com.phonegap.plugins.PushPlugin/www/PushNotification.js"></script>
4. The logic is written in notification.js It just has a button and a div element. The only thing to make sure here are that subscribeNotification() is called when the switch is on and unsubscribeNotification() is called when the switch is off. And on subscribe you will get a hex registration id in the function onNotification() and this registration id will be used in the push_register method.


### Manual Installation for Android

1) Install GCM support files

- copy the contents of `src/android/com/` to your project's `src/com/` folder.
- copy the contents of `libs/` to your `libs/` folder.
- copy `{android_sdk_path}/extras/android/support/v13/android-support-v13.jar` to your `libs/` folder.

The final hierarchy will likely look something like this:

	{project_folder}
		libs
			gcm.jar
			android-support-v13.jar
			cordova-3.4.0.jar
		src
			com
				plugin
					gcm
						CordovaGCMBroadcastReceiver.java
						GCMIntentService.java
						PushHandlerActivity.java
						PushPlugin.java
				{company_name}
					{intent_name}
						{intent_name}.java

2) Modify your `AndroidManifest.xml` and add the following lines to your manifest tag:

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<permission android:name="$PACKAGE_NAME.permission.C2D_MESSAGE" android:protectionLevel="signature" />
<uses-permission android:name="$PACKAGE_NAME.permission.C2D_MESSAGE" />
```

3) Modify your `AndroidManifest.xml` and add the following **activity**, **receiver** and **service** tags to your **application** section. (See the Sample_AndroidManifest.xml file in the Example folder.)

```xml
<activity android:name="com.plugin.gcm.PushHandlerActivity"/>
<receiver android:name="com.plugin.gcm.CordovaGCMBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND" >
	<intent-filter>
		<action android:name="com.google.android.c2dm.intent.RECEIVE" />
		<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
		<category android:name="$PACKAGE_NAME" />
	</intent-filter>
</receiver>
<service android:name="com.plugin.gcm.GCMIntentService" />
```

4) Check that the launch mode for the main Cordova Activity is one of the **[singleXXX](http://developer.android.com/guide/topics/manifest/activity-element.html#lmode)** options in **AndroidManifest.xml**.

```xml
<activity ... android:launchMode="singleTop">
```

Otherwise a new activity instance, with a new webview, will be created when activating the notifications.

5) Modify your `res/xml/config.xml` to include the following line in order to tell Cordova to include this plugin and where it can be found: (See the Sample_config.xml file in the Example folder)

```xml
<feature name="PushPlugin">
  <param name="android-package" value="com.plugin.gcm.PushPlugin" />
</feature>
```

6) Add the `PushNotification.js` script to your assets/www folder (or javascripts folder, wherever you want really) and reference it in your main index.html file. This file's usage is described in the **Plugin API** section below.

```html
<script type="text/javascript" charset="utf-8" src="PushNotification.js"></script>
```

PS: I'm using a few drawables (taken off some random website) for the icons in the notification tray, do copy them to the res\folder


Below are the methods for installing this plugin automatically using command line tools. For additional info, take a look at the [Plugman Documentation](https://github.com/apache/cordova-plugman/blob/master/README.md) and [Cordova Plugin Specification](https://github.com/alunny/cordova-plugin-spec).

**Note:** For each service supported - ADM, APNS, GCM or MPNS - you may need to download the SDK and other support files. See the [Manual Installation](#manual_installation) instructions below for more details about each platform.

### Cordova

The plugin can be installed via the Cordova command line interface:

1) Navigate to the root folder for your phonegap project. 2) Run the command.

```sh
cordova plugin add https://github.com/phonegap-build/PushPlugin.git
```

### Phonegap

The plugin can be installed using the Phonegap command line interface:

1) Navigate to the root folder for your phonegap project. 2) Run the command.

```sh
phonegap local plugin add https://github.com/phonegap-build/PushPlugin.git
```

### Plugman

The plugin is based on [plugman](https://github.com/apache/cordova-plugman) and can be installed using the Plugman command line interface:

```sh
plugman install --platform [PLATFORM] --project [TARGET-PATH] --plugin [PLUGIN-PATH]

where
	[PLATFORM] = ios,android 
	[TARGET-PATH] = path to folder containing your phonegap project
	[PLUGIN-PATH] = path to folder containing this plugin
```


##<a name="plugin_api"></a> Plugin API

In the plugin `examples` folder you will find a sample implementation showing how to interact with the PushPlugin. Modify it to suit your needs.

#### pushNotification
The plugin instance variable.

```js
var pushNotification;

document.addEventListener("deviceready", function(){
    pushNotification = window.plugins.pushNotification;
    ...
});
```

#### register
To be called as soon as the device becomes ready.

```js
$("#app-status-ul").append('<li>registering ' + device.platform + '</li>');
if ( device.platform == 'android' || device.platform == 'Android' ){
    pushNotification.register(
    successHandler,
    errorHandler,
    {
        "senderID":"replace_with_sender_id",
        "ecb":"onNotification"
    });
} else {
    pushNotification.register(
    tokenHandler,
    errorHandler,
    {
        "badge":"true",
        "sound":"true",
        "alert":"true",
        "ecb":"onNotificationAPN"
    });
}
```

On success, you will get a call to tokenHandler (iOS), onNotification (Android ), allowing you to obtain the device token or registration ID, or push channel name and Uri respectively. Those values will typically get posted to your intermediary push server so it knows who it can send notifications to.

***Note***

- **Android**: If you have not already done so, you'll need to set up a Google API project, to generate your senderID. [Follow these steps](http://developer.android.com/guide/google/gcm/gs.html) to do so. This is described more fully in the **Testing** section below. In this example, be sure and substitute your own senderID. Get your senderID by signing into to your [google dashboard](https://code.google.com/apis/console/). The senderID is found at *Overview->Dashboard->Project Number*.


#### successHandler
Called when a plugin method returns without error

```js
// result contains any message sent from the plugin call
function successHandler (result) {
	alert('result = ' + result);
}
```

#### errorHandler
Called when the plugin returns an error

```js
// result contains any error description text returned from the plugin call
function errorHandler (error) {
	alert('error = ' + error);
}
```

#### ecb ( Android and iOS)
Event callback that gets called when your device receives a notification

```js
// iOS
function onNotificationAPN (event) {
	if ( event.alert )
	{
		navigator.notification.alert(event.alert);
	}

	if ( event.sound )
	{
		var snd = new Media(event.sound);
		snd.play();
	}

	if ( event.badge )
	{
		pushNotification.setApplicationIconBadgeNumber(successHandler, errorHandler, event.badge);
	}
}
```

```js
// Android 
function onNotification(e) {
	$("#app-status-ul").append('<li>EVENT -> RECEIVED:' + e.event + '</li>');

	switch( e.event )
	{
	case 'registered':
		if ( e.regid.length > 0 )
		{
			$("#app-status-ul").append('<li>REGISTERED -> REGID:' + e.regid + "</li>");
			// Your GCM push server needs to know the regID before it can push to this device
			// here is where you might want to send it the regID for later use.
			console.log("regID = " + e.regid);
		}
	break;

	case 'message':
		// if this flag is set, this notification happened while we were in the foreground.
		// you might want to play a sound to get the user's attention, throw up a dialog, etc.
		if ( e.foreground )
		{
			$("#app-status-ul").append('<li>--INLINE NOTIFICATION--' + '</li>');

			// on Android soundname is outside the payload.
			// On Amazon FireOS all custom attributes are contained within payload
			var soundfile = e.soundname || e.payload.sound;
			// if the notification contains a soundname, play it.
			var my_media = new Media("/android_asset/www/"+ soundfile);
			my_media.play();
		}
		else
		{  // otherwise we were launched because the user touched a notification in the notification tray.
			if ( e.coldstart )
			{
				$("#app-status-ul").append('<li>--COLDSTART NOTIFICATION--' + '</li>');
			}
			else
			{
				$("#app-status-ul").append('<li>-# Cordova Push Notifications Plugin for Android, iOS.



PS: I'm using a few drawables (taken off some random website) for the icons in the notification tray, do copy them to the res\folder


Below are the methods for installing this plugin automatically using command line tools. For additional info, take a look at the [Plugman Documentation](https://github.com/apache/cordova-plugman/blob/master/README.md) and [Cordova Plugin Specification](https://github.com/alunny/cordova-plugin-spec).

**Note:** For each service supported - APNS, GCM - you may need to download the SDK and other support files. See the [Manual Installation](#manual_installation) instructions below for more details about each platform.

### Cordova

The plugin can be installed via the Cordova command line interface:

1) Navigate to the root folder for your phonegap project. 2) Run the command.

```sh
cordova plugin add https://github.com/phonegap-build/PushPlugin.git
```

### Phonegap

The plugin can be installed using the Phonegap command line interface:

1) Navigate to the root folder for your phonegap project. 2) Run the command.

```sh
phonegap local plugin add https://github.com/phonegap-build/PushPlugin.git
```

### Plugman

The plugin is based on [plugman](https://github.com/apache/cordova-plugman) and can be installed using the Plugman command line interface:

```sh
plugman install --platform [PLATFORM] --project [TARGET-PATH] --plugin [PLUGIN-PATH]

where
	[PLATFORM] = ios, android
	[TARGET-PATH] = path to folder containing your phonegap project
	[PLUGIN-PATH] = path to folder containing this plugin
```


##<a name="plugin_api"></a> Plugin API

In the plugin `examples` folder you will find a sample implementation showing how to interact with the PushPlugin. Modify it to suit your needs.

#### pushNotification
The plugin instance variable.

```js
var pushNotification;

document.addEventListener("deviceready", function(){
    pushNotification = window.plugins.pushNotification;
    ...
});
```

#### register
To be called as soon as the device becomes ready.

```js
$("#app-status-ul").append('<li>registering ' + device.platform + '</li>');
if ( device.platform == 'android' || device.platform == 'Android' ){
    pushNotification.register(
    successHandler,
    errorHandler,
    {
        "senderID":"replace_with_sender_id",
        "ecb":"onNotification"
    });
} else {
    pushNotification.register(
    tokenHandler,
    errorHandler,
    {
        "badge":"true",
        "sound":"true",
        "alert":"true",
        "ecb":"onNotificationAPN"
    });
}
```

On success, you will get a call to tokenHandler (iOS), onNotification (Android ), allowing you to obtain the device token or registration ID, or push channel name and Uri respectively. Those values will typically get posted to your intermediary push server so it knows who it can send notifications to.

***Note***

- **Android**: If you have not already done so, you'll need to set up a Google API project, to generate your senderID. [Follow these steps](http://developer.android.com/guide/google/gcm/gs.html) to do so. This is described more fully in the **Testing** section below. In this example, be sure and substitute your own senderID. Get your senderID by signing into to your [google dashboard](https://code.google.com/apis/console/). The senderID is found at *Overview->Dashboard->Project Number*.


#### successHandler
Called when a plugin method returns without error

```js
// result contains any message sent from the plugin call
function successHandler (result) {
	alert('result = ' + result);
}
```

#### errorHandler
Called when the plugin returns an error

```js
// result contains any error description text returned from the plugin call
function errorHandler (error) {
	alert('error = ' + error);
}
```

#### ecb ( Android and iOS)
Event callback that gets called when your device receives a notification

```js
// iOS
function onNotificationAPN (event) {
	if ( event.alert )
	{
		navigator.notification.alert(event.alert);
	}

	if ( event.sound )
	{
		var snd = new Media(event.sound);
		snd.play();
	}

	if ( event.badge )
	{
		pushNotification.setApplicationIconBadgeNumber(successHandler, errorHandler, event.badge);
	}
}
```

```js
// Android 
function onNotification(e) {
	$("#app-status-ul").append('<li>EVENT -> RECEIVED:' + e.event + '</li>');

	switch( e.event )
	{
	case 'registered':
		if ( e.regid.length > 0 )
		{
			$("#app-status-ul").append('<li>REGISTERED -> REGID:' + e.regid + "</li>");
			// Your GCM push server needs to know the regID before it can push to this device
			// here is where you might want to send it the regID for later use.
			console.log("regID = " + e.regid);
		}
	break;

	case 'message':
		// if this flag is set, this notification happened while we were in the foreground.
		// you might want to play a sound to get the user's attention, throw up a dialog, etc.
		if ( e.foreground )
		{
			$("#app-status-ul").append('<li>--INLINE NOTIFICATION--' + '</li>');

			// on Android soundname is outside the payload.
			// On Amazon FireOS all custom attributes are contained within payload
			var soundfile = e.soundname || e.payload.sound;
			// if the notification contains a soundname, play it.
			var my_media = new Media("/android_asset/www/"+ soundfile);
			my_media.play();
		}
		else
		{  // otherwise we were launched because the user touched a notification in the notification tray.
			if ( e.coldstart )
			{
				$("#app-status-ul").append('<li>--COLDSTART NOTIFICATION--' + '</li>');
			}
			else
			{
				$("#app-status-ul").append('<li>--BACKGROUND NOTIFICATION--' + '</li>');
			}
		}

	   $("#app-status-ul").append('<li>MESSAGE -> MSG: ' + e.payload.message + '</li>');
           //Only works for GCM
	   $("#app-status-ul").append('<li>MESSAGE -> MSGCNT: ' + e.payload.msgcnt + '</li>');
	   //Only works on Amazon Fire OS
	   $status.append('<li>MESSAGE -> TIME: ' + e.payload.timeStamp + '</li>');
	break;

	case 'error':
		$("#app-status-ul").append('<li>ERROR -> MSG:' + e.msg + '</li>');
	break;

	default:
		$("#app-status-ul").append('<li>EVENT -> Unknown, an event was received and we do not know what it is</li>');
	break;
  }
}
```



Looking at the above message handling code for AndroidS, a few things bear explanation. Your app may receive a notification while it is active (INLINE). If you background the app by hitting the Home button on your device, you may later receive a status bar notification. Selecting that notification from the status will bring your app to the front and allow you to process the notification (BACKGROUND). Finally, should you completely exit the app by hitting the back button from the home page, you may still receive a notification. Touching that notification in the notification tray will relaunch your app and allow you to process the notification (COLDSTART). In this case the **coldstart** flag will be set on the incoming event. You can look at the **foreground** flag on the event to determine whether you are processing a background or an in-line notification. You may choose, for example to play a sound or show a dialog only for inline or coldstart notifications since the user has already been alerted via the status bar.


Since the Android notification data models are much more flexible than that of iOS, there may be additional elements beyond **message**. You can access those elements and any additional ones via the **payload** element. This means that if your data model should change in the future, there will be no need to change and recompile the plugin.



#### senderID (Android only)
This is the Google project ID you need to obtain by [registering your application](http://developer.android.com/guide/google/gcm/gs.html) for GCM


#### tokenHandler (iOS only)
Called when the device has registered with a unique device token.

```js
function tokenHandler (result) {
    // Your iOS push server needs to know the token before it can push to this device
    // here is where you might want to send it the token for later use.
    alert('device token = ' + result);
}
```

#### setApplicationIconBadgeNumber (iOS only)
Set the badge count visible when the app is not running

```js
pushNotification.setApplicationIconBadgeNumber(successCallback, errorCallback, badgeCount);
```

The `badgeCount` is an integer indicating what number should show up in the badge. Passing 0 will clear the badge.

#### unregister ( Android and iOS)
You will typically call this when your app is exiting, to cleanup any used resources. Its not strictly necessary to call it, and indeed it may be desireable to NOT call it if you are debugging your intermediarry push server. When you call unregister(), the current token for a particular device will get invalidated, and the next call to register() will return a new token. If you do NOT call unregister(), the last token will remain in effect until it is invalidated for some reason at the GCM/ADM side. Since such invalidations are beyond your control, its recommended that, in a production environment, that you have a matching unregister() call, for every call to register(), and that your server updates the devices' records each time.

```js
pushNotification.unregister(successHandler, errorHandler, options);
```
##<a name="testing"></a> Testing
The notification system consists of several interdependent components.

1. The client application which runs on a device and receives notifications.
2. The notification service provider ( APNS for Apple, GCM for Google)
3. Intermediary servers that collect device IDs from clients and push notifications through  APNS and GCM.

This plugin and its target Cordova application comprise the client application.The APNS and GCM infrastructure are maintained by Apple and Google, respectively. In order to send push notifications to your users, you would typically run an intermediary server or employ a 3rd party push service. This is true for all  APNS (iOS), GCM (Android)  notifications. However, when testing the notification client applications, it may be desirable to be able to push notifications directly from your desktop, without having to design and build those server's first. There are a number of solutions out there to allow you to push from a desktop machine, sans server.

### Testing APNS and GCM notifications

An easy solution to test APNS and GCM is a ruby gem called [pushmeup](http://rubygems.org/gems/pushmeup) (tested only on Mac, but it probably works fine on Windows as well).

#### Prerequisites:

- Ruby gems is installed and working.
- You have successfully built a client with this plugin, on both iOS and Android and have installed them on a device.
- You have installed the [PushMeUp gem](https://github.com/NicosKaralis/pushmeup): `$ sudo gem install pushmeup`



#### APNS/iOS Setup
[Follow this tutorial](http://www.raywenderlich.com/3443/apple-push-notification-services-tutorial-part-12) to create a file called ck.pem.

Start at the section entitled "Generating the Certificate Signing Request (CSR)", and substitute your own Bundle Identifier, and Description.

1. Go the this plugin's Example/server folder and open pushAPNS.rb in the text editor of your choice.
2. Set the APNS.pem variable to the path of the ck.pem file you just created
3. Set APNS.pass to the password associated with the certificate you just created. (warning this is cleartext, so don't share this file)
4. Set device_token to the token for the device you want to send a push to. (you can run the Cordova app / plugin in Xcode and extract the token from the log messages)
5. Save your changes.


#### Android/GCM Setup
[Follow these steps](http://developer.android.com/guide/google/gcm/gs.html) to generate a project ID and a server based API key.

1. Go the this plugin's Example/server folder and open pushGCM.rb in the text editor of your choice.
2. Set the GCM.key variable to the API key you just generated.
3. Set the destination variable to the Registration ID of the device. (you can run the Cordova app / plugin in on a device via Eclipse and extract the regID from the log messages)

####  Sending a test notification

1. cd to the directory containing the two .rb files we just edited.
2. Run the Cordova app / plugin on both the Android and iOS devices you used to obtain the regID  / device token, respectively.
3. `$ ruby pushGCM.rb` or `$ ruby pushAPNS.rb`

If you run this demo using the emulator you will not receive notifications from GCM. You need to run it on an actual device to receive messages or install the proper libraries on your emulator (You can follow [this guide](http://www.androidhive.info/2012/10/android-push-notifications-using-google-cloud-messaging-gcm-php-and-mysql/) under the section titled "Installing helper libraries and setting up the Emulator") If everything seems right and you are not receiving a registration id response back from Google, try uninstalling and reinstalling your app. That has worked for some devs out there.

While the data model for iOS is somewhat fixed, it should be noted that GCM is far more flexible. The Android implementation in this plugin, for example, assumes the incoming message will contain a '**message**' and a '**msgcnt**' node. This is reflected in both the plugin (see GCMIntentService.java) as well as in provided example ruby script (pushGCM.rb). Should you employ a commercial service, their data model may differ. As mentioned earlier, this is where you will want to take a look at the **payload** element of the message event. In addition to the cannonical message and msgcnt elements, any additional elements in the incoming JSON object will be accessible here, obviating the need to edit and recompile the plugin. Many thanks to Tobias Hößl for this functionality!


####  Sending a test notification

1. Inside the plugin's examples/server folder, open the `pushADM.js` NodeJS script with a text editor. (You should already have NodeJS installed).
2. Edit the CLIENT_ID and CLIENT_SECRET variables with the values from the ADM Security Profile page for your app. This will allow your app to securely identify itself to Amazon services.
3. Compile and run the sample app on your device. Note the sample app requires the Cordova Device and Media plugins to work.
4. The sample app will display your device's registration ID. Copy that value (it's very long) from your device into `pushADM.js`, entered in the REGISTRATION_IDS array. To test sending messages to more than one device, you can enter in multiple REGISTRATION_IDS into the array.
5. To send a test push notification, run the test script via a command line using NodeJS: `$ node pushADM.js`.



##<a name="additional_resources"></a>Additional Resources

- [Apple Push Notification Services Tutorial: Part 1/2](http://www.raywenderlich.com/3443/apple-push-notification-services-tutorial-part-12)
- [Apple Push Notification Services Tutorial: Part 2/2](http://www.raywenderlich.com/3525/apple-push-notification-services-tutorial-part-2)
- [Google Cloud Messaging for Android](http://developer.android.com/guide/google/gcm/index.html) (Android)
- [How to Implement Push Notifications for Android](http://tokudu.com/2010/how-to-implement-push-notifications-for-android/)
- [Local and Push Notification Programming Guide](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) (Apple)


##<a name="acknowledgments"><a/> Acknowledgments

Huge thanks to Mark Nutter whose [GCM-Cordova plugin](https://github.com/marknutter/GCM-Cordova) forms the basis for the Android side implimentation.

Likewise, the iOS side was inspired by Olivier Louvignes' [Cordova PushNotification Plugin](https://github.com/phonegap/phonegap-plugins/tree/master/iOS/PushNotification) (Copyright (c) 2012 Olivier Louvignes) for iOS.

Props to [Tobias Hößl](https://github.com/CatoTH), who provided the code to surface the full JSON object up to the JS layer.

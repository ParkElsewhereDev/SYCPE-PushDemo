# THIS RESPOSITORY IS NOW PUBLIC

In `code/investigations/` are two projects. They are the yin and yang, the fore and aft, the salt and vinegar, of a key exchange framework using push messaging.

## SYCPE-QR-Dummy

From the terminal, issue the command

`cordova run`

This will open a browser (in all likelihood sending your data without your tacit permission to Google) and show a QR Code, which is a textual UUID. This UUID is displayed as text above the QR Code image.

## SYCPE-Starter

This is a simple Cordova Android project which signs in to Firebase Cloud Messaging.

Build it for an Android device with the terminal command

`cordova run android --device`

(it is not supported on iOS or any other platform).

If it cannot successfully sign in to FCM, there will be a 'retry' button at the top of the interface - tap it to try again, the application is fairly useless without a successful sign-in.

After this, tap 'Scan QR Code' to scan a QR code as provided from `SYCPE-QR-Dummy`. The text of this code will be subscribed to as a *topic* from Firebase Cloud Messaging. 

If a message is posted to this topic using a combination of the FCM console and a HTTP Post tool, it will be picked up by the application and displayed as a JavaScript `alert()`.

## Testing using VSCode

You will need the *vscode-http-client* extension for VSCode to run this test. This will allow you to send a message to all devices subscribed to a single topic.

You will need the Firebase Cloud Messaging Server Key, which can be gotten from the FCM Console, under `Settings`->`Cloud Messaging` tab, as `Server Key` (remember to never release this key to a public location, as you cannot generate a new one - you will need to create a new application!)

In the VSCide *vscode-http-client*, set the following properties:

### Request Settings

Parameter | Value
----------|--------
URL| `https://fcm.googleapis.com/fcm/send`
Body | `{"message":"hello",\n"title":"Hello There",\n"to":"/topics/`*topic_id*`"}`

Where *topic_id* is the UUID generated earlier, in the form of something like *2a4abbfc-13be-4253-83b5-a899cce8b89a*. Be aware this is case sensitive!

### Custom Headers

Name | Value
-----|--------
Content-Type | `application/json`
Authorisation | `key=`*FCM_key*

Where *FCM_key* is the Firebase Cloud Messaging Server Key taken from the Firebase console earlier.

**WARNING** To avoid creating a new stack of infrastructure, this application is internally (in the `config.xml` file) named as `uk.ac.mmu.digitallabs.rs.acknack` - do not rename the application as there will need to be a new `google-services.xml` file created, which will issue a new server key, which will require a new Heroku instance setting up, configuring and deploying. 

You may also find devices with an application with the *same name* but *different functionality* are in DigitalLabs. You may need to uninstall the other app to be able to deploy this one properly.

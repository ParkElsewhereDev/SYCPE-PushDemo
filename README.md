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

Because of the need to embed a *server_key*, you will need to communicate through a secured network-based server to handle the actual sending of messages; through this can be done on a device (as it is ultimately a HTTP request), it requires embedding this key in the app, which is a huge security hole.

Using the Swagger API gives us this internet-hosted middleware and means we do not have to worry about securing the key. 

At the end of this document is an example of what happens to interact directly with the FCM server - this is provided to show the communications at a low-level, and should *never* be used from a client due to the security issues discussed above.

### Testing using the Swagger API

Set the *URL* to `https://rescuestationpush.herokuapp.com:443` - this is our gateway server that holds the FCM server key, so it does not need to be embedded in an application.

Use the following as a skeleton for the `Body` field.

`{
  "recipient_id":"/topics/`*topic_id*`",
  "sender_id": "",
  "message_id": "",
  "message_type": 0,
  "sender_role": 0,
  "payload": "{\"any-old-data\":\"any-any-any-old-data\"}"
}`

Set *topic_id* to be the UUID you created earlier (*watch out for case sensitivity!*).

This will send any payload you care to the recipient device(s) that are subscribed to the specified topic. The *`payload`* is the field that will carry your data through, and though you can put set of key and values (even nested ones), you will need to be sure you encode them correctly and escape inline quotes (which will be necessary) with backslashes.

### Testing using direct FCM interaction

You will need the Firebase Cloud Messaging Server Key, which can be gotten from the FCM Console, under `Settings`->`Cloud Messaging` tab, as `Server Key` (remember to never release this key to a public location, as you cannot generate a new one - you will need to create a new application!)

In the VSide *vscode-http-client*, set the following properties:

#### Request Settings

Parameter | Value
----------|--------
URL| `https://fcm.googleapis.com/fcm/send`
Body | `{"message":"hello", "title":"Hello There", "to":"/topics/`*topic_id*`"}`

Where *topic_id* is the UUID generated earlier, in the form of something like *2a4abbfc-13be-4253-83b5-a899cce8b89a*. Be aware this is case sensitive!

#### Custom Headers

Name | Value
-----|--------
Content-Type | `application/json`
Authorization | `key=`*FCM_key*

Where *FCM_key* is the Firebase Cloud Messaging Server Key taken from the Firebase console earlier.

**WARNING** To avoid creating a new stack of infrastructure, this application is internally (in the `config.xml` file) named as `uk.ac.mmu.digitallabs.rs.acknack` - do not rename the application as there will need to be a new `google-services.xml` file created, which will issue a new server key, which will require a new Heroku instance setting up, configuring and deploying. 

You may also find devices with an application with the *same name* but *different functionality* are in DigitalLabs. You may need to uninstall the other app to be able to deploy this one properly.

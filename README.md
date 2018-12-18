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

**WARNING** To avoid creating a new stack of infrastructure, this application is internally (in the `config.xml` file) named as `uk.ac.mmu.digitallabs.rs.acknack` - do not rename the application as there will need to be a new `google-services.xml` file created, which will issue a new server key, which will require a new Heroku instance setting up, configuring and deploying. 

You may also find devices with an application with the *same name* but *different functionality* are in DigitalLabs. You may need to uninstall the other app to be able to deploy this one properly.